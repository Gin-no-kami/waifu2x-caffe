# Build on Ubuntu 16.04

## Build caffe for waifu2x-caffe

```
git clone -b waifu2x-caffe-ubuntu https://github.com/nagadomi/caffe.git lltcggie-caffe
cd lltcggie-caffe
cp Makefile.config.example-ubuntu Makefile.config
# edit Makefile.config
make
```

When using cuDNN, set `USE_CUDNN := 1` in `Makefile.config`.

## Build waifu2x-caffe

(I tested on Ubuntu18.04 + CUDA10.1 + CuDNN 7.5 + GTX 1080)

```sh
git clone -b ubuntu https://github.com/nagadomi/waifu2x-caffe.git
cd waifu2x-caffe
git submodule update --init --recursive

# create symlink to ltcggie-caffe
ln -s ../lltcggie-caffe ./caffe
ln -s ../lltcggie-caffe ./libcaffe

# build
rm -fr build # clean
mkdir build
cd build
cmake .. -DCUDA_NVCC_FLAGS="-D_FORCE_INLINES  -gencode arch=compute_61,code=sm_61 " # sm_61 is for GTX1080
make
ln -s `realpath ./waifu2x-caffe` ../bin
```

When you got `cuDNN Not Found` issue,
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
