# darknet_truck
用darknet识别卡车 汽车 巴士和人案例

export PATH=/usr/local/cuda-8.0/bin/:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:$LD_LIBRARY_PATH

train
./darknet detector train cfg/voc.data cfg/yolo-voc.2.0.cfg cfg/darknet19_448.conv.23

test
./darknet detector test cfg/voc.data cfg/yolo-voc.2.0.cfg backup/yolo-voc_900.weights validateImage/043.jpg
