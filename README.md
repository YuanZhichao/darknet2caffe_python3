# darknet2caffe_python3
convert dartnet model to caffe (python3 version)
# Requirements
  
  Python3.7

  Caffe

  Pytorch >= 0.4.0
# Add Caffe Layers
1. Copy `caffe_layers/mish_layer/mish_layer.hpp, caffe_layers/upsample_layer/upsample_layer.hpp` into `include/caffe/layers/`.
2. Copy `caffe_layers/mish_layer/mish_layer.cpp mish_layer.cu, caffe_layers/upsample_layer/upsample_layer.cpp upsample_layer.cu` into `src/caffe/layers/`.
3. Copy `caffe_layers/pooling_layer/pooling_layer.cpp` into `src/caffe/layers/`.Note:only work for yolov3-tiny,use with caution.
4. Add below code into `src/caffe/proto/caffe.proto`.

```
// LayerParameter next available layer-specific ID: 147 (last added: recurrent_param)
message LayerParameter {
  optional TileParameter tile_param = 138;
  optional VideoDataParameter video_data_param = 207;
  optional WindowDataParameter window_data_param = 129;
++optional UpsampleParameter upsample_param = 149; //added by chen for Yolov3, make sure this id 149 not the same as before.
++optional MishParameter mish_param = 150; //added by chen for yolov4,make sure this id 150 not the same as before.
}

// added by chen for YoloV3
++message UpsampleParameter{
++  optional int32 scale = 1 [default = 1];
++}

// Message that stores parameters used by MishLayer
++message MishParameter {
++  enum Engine {
++    DEFAULT = 0;
++    CAFFE = 1;
++    CUDNN = 2;
++  }
++  optional Engine engine = 2 [default = DEFAULT];
++}
```
5.remake caffe.

# Demo
  $ python cfg[in] weights[in] prototxt[out] caffemodel[out]
  
  Example
```
python darknet2caffe.py cfg/yolov4.cfg weights/yolov4.weights prototxt/yolov4.prototxt caffemodel/yolov4.caffemodel
```
  or edit the bash file and
```
sh convert.sh
```
  partial log as below.
```
...
I0322 01:29:49.497282 75672 net.cpp:202] layer3-conv does not need backward computation.
I0322 01:29:49.497308 75672 net.cpp:202] layer2-maxpool does not need backward computation.
I0322 01:29:49.497331 75672 net.cpp:202] layer1-act does not need backward computation.
I0322 01:29:49.497356 75672 net.cpp:202] layer1-scale does not need backward computation.
I0322 01:29:49.497380 75672 net.cpp:202] layer1-bn does not need backward computation.
I0322 01:29:49.497404 75672 net.cpp:202] layer1-conv does not need backward computation.
I0322 01:29:49.497429 75672 net.cpp:202] input does not need backward computation.
I0322 01:29:49.497453 75672 net.cpp:244] This network produces output layer16-conv
I0322 01:29:49.497514 75672 net.cpp:244] This network produces output layer23-conv
I0322 01:29:49.497550 75672 net.cpp:257] Network initialization done.
unknow layer type yolo 
save prototxt to yolov3-tiny.prototxt
save caffemodel to yolov3-tiny.caffemodel
```
