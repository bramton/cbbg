---
title: "Boosting PointNet++ performance"
date: 2021-12-08T11:55:53+01:00
draft: false
---

PointNet++ has been a major milestone in the area of applying deep learning to point clouds. Though impressive results can be obtained, the computational load, especially on the CPU side is high. Luckily, after a quick internet search an elegant solution was found which can speed things up by a factor two. Credits for this solution go to the blog post by Open3D. During the interpolation stage PointNet++ uses a nearest neighbout search, the original implementation uses a brute force approach to finding nearest neighbours. The solution put forward by Open3D is by using a *k*-d tree to speed up the nearest neighbour search. This post will show how this can be implemented using the nanoflann library. This library is a C++ header only library for building *k*-d trees.

# Implementation
The slowness culprit is the [threenn_cpu](https://github.com/charlesq34/pointnet2/blob/42926632a3c33461aebfbee2d829098b30a23aaa/tf_ops/3d_interpolation/tf_interpolate.cpp#L60) function. By rewriting this function a speed up of 2x during training can be achieved. First step is to write a very thin wrapper around the point cloud data which provides a standard interface for the nanoflann library. This thin wrapper provides methods to get the total number of points and get the point coordinates at a certain index. The next step is to build the tree and query it to get the nearest neighbours.

## Simple point cloud wrapper
Note that the number of dimensions is hardcoded to three. Also the `kdtree_get_bbox` returns false which means that the library will calculate the bounding box itself. If it is known at forehand this function can return the bounding box and save some computation time that way.

``` CPP
struct PointCloud {
    const float *pts;
    const size_t n;

    PointCloud(const float *pts, size_t n):pts(pts), n(n) { }

    inline size_t kdtree_get_point_count() const { return n; }

    inline float kdtree_get_pt(const size_t idx, const size_t dim) const {
        return pts[idx*3 + dim];
    }

    template <class BBOX>
    bool kdtree_get_bbox(BBOX &bb) const {
        return false;
    }
};
```

## *k*-d tree search implementation
For interpolation, PointNet++ needs to query an input point cloud and return the three nearest neighbours of the query point. In this snippet `b` is the batch dimension, `n` is the length of the query points and `m` is the length of the input point cloud. Note that in this case n > m, for instance n=1024 and m=256.

``` CPP
void threenn_cpu(int b, int n, int m,
                 const float *qpoints, const float *dpoints,
                 float *dist, int *idx)
{
    using namespace nanoflann;
    const size_t dims = 3;
    const unsigned int max_leaf = 20;

    typedef KDTreeSingleIndexAdaptor<
        L2_Simple_Adaptor<float, PointCloud>,
        PointCloud,
        dims
        > my_kd_tree_t;

    const size_t NR = 3; // Number of Results
    size_t num_valid;
    unsigned int out_idx[NR];
    float out_dist[NR];

    for (int bidx = 0; bidx < b; ++bidx) {
        PointCloud cloud(&dpoints[bidx*m*3], m);
        my_kd_tree_t tree(dims, cloud,
            KDTreeSingleIndexAdaptorParams(max_leaf) );
        tree.buildIndex();

        for (int qidx = 0; qidx < n; ++qidx) {
            num_valid = tree.knnSearch(&qpoints[bidx*n*3 + qidx*3], NR,
                &out_idx[0], &out_dist[0]);
            int base_idx = bidx*n*NR + qidx*NR;
            idx[base_idx + 0] = out_idx[0];
            idx[base_idx + 1] = out_idx[1];
            idx[base_idx + 2] = out_idx[2];
            dist[base_idx + 0] = out_dist[0];
            dist[base_idx + 1] = out_dist[1];
            dist[base_idx + 2] = out_dist[2];
        }
    }
}
```

And finally the result, the first line shows with the brute force approach and the second line shows with the *k*-d tree approach. It is indeed roughly a factor two gain in training time :)

```
4/4 [==============================] - 6s 2s/step - loss: 2.1883 - sparse_categorical_accuracy: 0.3334 - updated_mean_io_u: 0.0253
4/4 [==============================] - 4s 896ms/step - loss: 2.3116 - sparse_categorical_accuracy: 0.1792 - updated_mean_io_u: 0.0136
```

# References
* [Open3D blog post on semantic segmentation of point clouds](http://www.open3d.org/2019/01/16/on-point-clouds-semantic-segmentation/)
* [nanoflann library](https://github.com/jlblancoc/nanoflann)
