# Project Write-Up

# Deploy a People Counter App at the Edge

The people counter application will demonstrate how to create a smart video IoT solution using Intel® hardware and software tools. The app will detect people in a designated area, providing the number of people in the frame, average duration of people in frame, and total count.

## Explaining Custom Layers and Model Selection

The process behind converting custom layers involves the following:

TensorFlow Object Detection Model Zoo (https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md) contains many pre-trained models on the coco dataset. 

We have used two models in our project : ssd_inception_v2_coco and ssd_mobilenet_v2_coco performed good and are fast in detecting people with less errors. 

Intel openVINO already contains extensions for custom layers used in TensorFlow Object Detection Model Zoo.

We can Download the model from the GitHub repository of Tensorflow Object Detection Model Zoo by the following command:

wget http://download.tensorflow.org/models/object_detection/ssd_inception_v2_coco_2018_01_28.tar.gz

We then extract the tar file and cd to the directory and then run the command for model conversion to IR, all these steps are described below:

Extraction: tar -xvf ssd_inception_v2_coco_2018_01_28.tar.gz
CD: cd ssd_inception_v2_coco_2018_01_28

Conversion to IR: python /opt/intel/openvino/deployment_tools/model_optimizer/mo.py --input_model frozen_inference_graph.pb --tensorflow_object_detection_api_pipeline_config pipeline.config --reverse_input_channels --tensorflow_use_custom_operations_config /opt/intel/openvino/deployment_tools/model_optimizer/extensions/front/tf/faster_rcnn_support.json

Similar work is done for other model too.

## Comparing Model Performance

Upon comparing the model performance after running experiments, it was clearly observed that Latency and Memory decreases in case of OpenVINO Models as compared to plain Tensorflow models and this is very helpful in OpenVINO applications.

## Assess Model Use Cases

Some of the potential use cases of the people counter app are checking/counting the number of people in a given assembled area where social restrictions are imposed eg: say Curfue. This app can be deployed to check the total number of people and then give warnings if any violations. This can be very efficiently extended and related to the current pandemic situation COVID-19 that is prevailing the whole world.

This use cases would be useful because it can efficiently help in implementing strict social distancing and curfue in areas of Lockdown imposed by government.

## Assess Effects on End User Needs

When it comes to end user concerns, since Edge Computing is regarded as ideal for operations with extreme latency concerns, so, medium scale companies with budget limitations can use edge computing to save financial resources.

## Model Research


In investigating potential people counter models, I tried each of the following three models:

- Model 1: ssd_inception_v2_coco_2018_01_28
  - Model Source: https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md -- select ssd_inception_v2_coco_2018_01_28

  - I converted the model to an Intermediate Representation with the following arguments : python /opt/intel/openvino/deployment_tools/model_optimizer/mo.py --input_model ssd_inception_v2_coco_2018_01_28/frozen_inference_graph.pb --tensorflow_object_detection_api_pipeline_config pipeline.config --reverse_input_channels --tensorflow_use_custom_operations_config /opt/intel/openvino/deployment_tools/model_optimizer/extensions/front/tf/ssd_v2_support.json

 
  
- Model 2: ssd_mobilenet_v2_coco_2018_03_29
  - Model Source: https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md -- select ssd_mobilenet_v2_coco_2018_03_29

  - I converted the model to an Intermediate Representation with the following arguments python /opt/intel/openvino/deployment_tools/model_optimizer/mo.py --input_model ssd_mobilenet_v2_coco_2018_03_2/frozen_inference_graph.pb --tensorflow_object_detection_api_pipeline_config pipeline.config --reverse_input_channels --tensorflow_use_custom_operations_config /opt/intel/openvino/deployment_tools/model_optimizer/extensions/front/tf/ssd_v2_support.json
  

### Running the People Counter Application
I have followed below steps to run the application:

Environment setup: source /opt/intel/openvino/bin/setupvars.sh -pyver 3.5

Start Mosca Server: cd webservice/server/node-server
		    node ./server.js

Start GUI: cd webservice/ui
	   npm run dev

FFMPEG Server: sudo ffserver -f ./ffmpeg/server.conf

Run the main code: 
1. ssd_inception_v2:  python main.py -i resources/Pedestrian_Detect_2_1_1.mp4 -m ssd_inception_v2_coco_2018_01_28/frozen_inference_graph.xml -l /opt/intel/openvino/deployment_tools/inference_engine/lib/intel64/libcpu_extension_sse4.so -d CPU -pt 0.4 | ffmpeg -v warning -f rawvideo -pixel_format bgr24 -video_size 768x432 -framerate 24 -i - http://0.0.0.0:3004/fac.ffm

2. ssd_mobilenet_v2:  python main.py -i resources/Pedestrian_Detect_2_1_1.mp4 -m ssd_mobilenet_v2_coco_2018_03_29/frozen_inference_graph.xml -l /opt/intel/openvino/deployment_tools/inference_engine/lib/intel64/libcpu_extension_sse4.so -d CPU -pt 0.4 | ffmpeg -v warning -f rawvideo -pixel_format bgr24 -video_size 768x432 -framerate 24 -i - http://0.0.0.0:3004/fac.ffm


### Output Screenshots





