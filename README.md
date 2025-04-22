# dashcam-depth-estimation
Simplified method for depth estimation through object detection model and camera properties

#Key References

**YOLOv7:** https://github.com/WongKinYiu/yolov7.git 

**Fine-Tuning Tutorial:** https://learnopencv.com/fine-tuning-yolov7-on-custom-dataset/#The-Training-Experiments-that-We-Will-Carry-Out

**Dataset: KITTI Vision Benchmark Suite, 2D Object Detection Evaluation:** https://www.cvlibs.net/datasets/kitti/eval_object.php?obj_benchmark=2d


# Hardware / Software Requirements

Nvidia GPU for local training, or cloud platforms like Google Colab or Vertex AI

Camera with known focal length and pixel size

Approximate dimensions of objects of interest (e.g., cars, trucks, vans)

# Dataset Preparation

The KITTI Dataset has 8 classes, we want to focus on 3 of them, Car, Truck, and Van. Future work will focus on incorporating additional classes.

To prepare the dataset, run preprocess-yolo-format.ipynb. This notebook will split the dataset into training, testing, and validation sets, and convert labels into YOLO-compatible format

# Fine Tuning

Running the yolov7_fine_tuning.ipynb file will clone the YOLOv7 github and install the requirements.

```
git clone https://github.com/WongKinYiu/yolov7.git
cd yolov7
!pip install -r requirements.txt
```

Train the model with the current weights and the train.py script included in the YOLOv7 github. 

```
!python train.py --epochs 20 --workers 2 --device 0 --batch-size 16 \
--data data/vehicles.yaml --img 1242 375 --cfg cfg/training/yolov7_vehicles-tiny.yaml \
--weights 'yolov7-tiny.pt' --name yolov7_tiny_vehicles_fixed_res --hyp data/hyp.scratch.tiny.yaml
```

This will generate custom weights and evaluation results.

![results(1)](https://github.com/user-attachments/assets/47f21f48-7c8a-4601-925b-2ab0b7b3f883)

# Inference and Testing

The formula we use to determine depth is based on the pixel size and focal length of the camera.

![Capture](https://github.com/user-attachments/assets/8a5fd143-fece-48f9-bd07-e7f6d5b5516b)

This formula is implemented in the detect_update.py, which is then used in video_inference.ipynb to perform depth estimation of road vehicles on a video feed.

```
!python detect_update.py --weights C:/Schoolwork/CS585/Project/best.pt --source C:/Schoolwork/CS585/Project/video_candidates/phone_inference1.mp4 \
--save-txt
```

This will output object detections and estimated depths frame-by-frame.
