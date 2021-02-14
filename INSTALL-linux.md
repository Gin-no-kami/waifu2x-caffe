# Build on Arch

## Packges to install
cuda, cudnn, opencv, boost, gflags, google-glog, leveldb, cmake, protobuf, lmdb, python-numpy, hdf5

## Build caffe for waifu2x-caffe

```
git clone https://github.com/Gin-no-kami/caffe.git caffe
cd caffe
cp Makefile.config.example-ubuntu Makefile.config
# edit Makefile.config
make
```

When using cuDNN, set `USE_CUDNN := 1` in `Makefile.config`.

## Build waifu2x-caffe

(I tested using CUDA11.1 + CuDNN 8.0 + RTX3080)

```sh
git clone https://github.com/Gin-no-kami/waifu2x-caffe.git
cd waifu2x-caffe
git submodule update --init --recursive

# create symlink to caffe
ln -s ../caffe ./caffe
ln -s ../caffe ./libcaffe

# cmake
rm -fr build # clean
mkdir build
cd build
cmake .. -DCUDA_NVCC_FLAGS="-D_FORCE_INLINES  -gencode arch=compute_86,code=sm_86 " # sm_86 is for RTX3080
# Edit the file build/libcaffe/src/caffe/CMakeFiles/caffe.dir/flags.make, removing from the CUDA_FLAGS variable.
# If you know how to alter cmake to not put these in there let me know and I will update the cmake file
-Xcudafe --diag_suppress=set_but_not_used -DBOOST_ALL_NO_LIB;-DUSE_LMDB;-DUSE_LEVELDB;-DUSE_CUDNN;-DUSE_OPENCV;-DWITH_PYTHON_LAYER

# build
make
ln -s `realpath ./waifu2x-caffe` ../bin
```

If you got `cuDNN Not Found` issue,
```
cuDNN             :   Not Found
```
re-run cmake command. (Not sure, but it can be solved with re-run cmake command).

## Run
```
cd bin
./waifu2x-caffe -p cudnn -m scale -i input.png -o out.png | nkf
./waifu2x-caffe -p cuda -m scale -i input.png -o out.png | nkf
./waifu2x-caffe -p cpu -m scale -i input.png -o out.png | nkf
```
