# Basic Functioning

- Visual Language Model (VLM) is used to generate captions for each detected and segmented object in the scene. These captions describe the objects semantically, bridging visual data with natural language.

- Large Language Model (LLM) takes pairs of captions as input to infer **relationships between objects** (e.g., spatial relationships like "on top of," "next to," or semantic connections like "part of"). This step creates a **scene graph** where:
    - Nodes represent objects (with captions).
    - Edges represent relationships between these objects.

- Scene Graph can be Queried through an **LLM**, enabling natural language interactions to extract information about the scene (e.g., "What objects are on the table?" or "Describe the layout of the room").

This succinctly captures the essence of the system and emphasizes its capability to model and interact with complex scenes in a human-understandable way.

# Input of Concept Graph
```
- RGB image
- Depth image
- Pose
- Camera Intrinsics
```
# Output of Concept Graph
```            
# Convert the detections to a dict. The elements are in np.array
results = {
            # add new uuid for each detection 
            "xyxy": curr_det.xyxy,
            "confidence": curr_det.confidence,
            "class_id": curr_det.class_id,
            "mask": curr_det.mask,
            "classes": obj_classes.get_classes_arr(),
            "image_crops": image_crops,
            "image_feats": image_feats,
            "text_feats": text_feats,
            "detection_class_labels": detection_class_labels,
            "labels": labels,
            "edges": edges,
            "captions": captions,
            }
```

This script performs **3D mapping of objects detected in images** while integrating multiple advanced AI models. Here's a breakdown of what it does and how **SAM**, **YOLOv8**, and **CLIP** are used:

---

### Overview
The script is designed for grounded 3D object detection, segmentation, and scene understanding. It processes input RGB-D datasets (RGB images and depth maps) to:

1. Detect objects using YOLOv8.
2. Segment objects using the Segment Anything Model (SAM).
3. Classify and describe objects using CLIP features for semantic understanding.
4. Map objects in 3D space using depth information.
5. Generate relationships between objects based on visual, spatial, and text-based similarity.

### Key Components
1. Mobile-SAM: 
   - SAM (Segment Anything Model) segments regions in the image corresponding to detected objects.
   - Mobile-SAM is optimized for smaller devices and is computationally lightweight while maintaining good performance.
   - SAM works by taking bounding boxes from YOLO and generating pixel-wise masks for objects.

2. YOLOv8:
   - YOLO (You Only Look Once) is used for **object detection** (bounding boxes and class labels).
   - This script uses `yolov8l-world.pt`, which is likely a YOLOv8 model fine-tuned for detecting objects in a **real-world setting**.

3. CLIP:
   - CLIP (Contrastive Language–Image Pretraining) is used for:
     - Extracting **visual features** of objects.
     - Capturing **semantic relationships** between objects and generating captions.
   - CLIP is integrated to compute **text and visual features** for each segmented object, enabling the generation of natural language descriptions and relationships.

4. 3D Mapping:
   - The detected and segmented objects are placed in a **3D space** using the depth information.
   - The **poses of the camera** are used to align objects across frames.
   - Point clouds are created for each object to represent their shape in 3D.

5. Scene Understanding and Relationships:
   - Relationships between objects (e.g., spatial, visual, or textual similarity) are modeled as edges in a scene graph.
   - GPT-based methods (like GPT-4 vision or OpenAI API) are used for refining captions and object relationships.

---

### Pipeline Steps
1. Data Loading: RGB images, depth maps, and camera poses are loaded from the dataset.
2. Object Detection: YOLO detects bounding boxes and classifies objects.
3. Object Segmentation: SAM generates masks for the detected objects.
4. Feature Extraction: CLIP extracts features for objects, including visual embeddings, text embeddings, and potential labels.
5. 3D Point Cloud Creation:
   - Depth maps and masks are used to generate point clouds for each object.
   - Objects are placed in a 3D map using the camera pose.

6. Relationship Modeling:
   - Relationships (spatial, visual, and textual) between objects are computed.
   - Captions are consolidated and relationships are visualized.

7. Post-Processing:
   - Objects are filtered, merged, and refined.
   - Point clouds and metadata are saved for visualization and further analysis.

### Outputs
1. 3D Object Point Clouds: Each object is represented as a 3D point cloud.
2. Scene Graph: Captures relationships between objects (e.g., "chair next to the table").
3. Visualizations:
   - Annotated images with bounding boxes and masks.
   - Visualizations of object relationships and captions.
4. Saved Metadata: Object point clouds, bounding boxes, and class labels are stored for later use.
