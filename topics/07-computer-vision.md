# Computer Vision

[⬅ Back to index](../README.md)

Covers: convolutions, pooling, receptive fields, classic CNN architectures (LeNet → ResNet → EfficientNet), vision transformers, transfer learning, data augmentation, object detection (R-CNN family, YOLO, DETR), segmentation (semantic, instance, SAM), CLIP and vision-language models, and deploying vision models on devices.

---

### 1. How does a convolutional layer work, and why is it better than a dense layer for images?

<details>
<summary>Answer</summary>

A convolution slides small learned **filters** (e.g. 3×3) across the image, computing dot products to produce feature maps. Key properties:
- **Local connectivity** – each output looks at a small neighbourhood (pixels near each other are related).
- **Weight sharing** – the same filter is used everywhere, so far fewer parameters than a dense layer, and features are detected anywhere (**translation equivariance**).
- **Hierarchy** – early layers learn edges and textures, deeper layers learn parts and objects.

Output size: `(W − K + 2P)/S + 1` for input width W, kernel K, padding P, stride S.

</details>

### 2. What do pooling, stride and padding do? What is a receptive field?

<details>
<summary>Answer</summary>

- **Pooling** (max or average) downsamples feature maps, adding some translation invariance and reducing compute.
- **Stride** – step size of the filter; stride 2 halves the spatial size.
- **Padding** – adds borders (usually zeros) so outputs keep their size and edges are used.
- **Receptive field** – the region of the input that influences one output unit. It grows with depth, kernel size, stride and dilation; deep layers "see" large parts of the image.

</details>

### 3. Walk through the evolution of CNN architectures.

<details>
<summary>Answer</summary>

- **LeNet** (1998) – early CNN for digits.
- **AlexNet** (2012) – ReLU, dropout, GPUs; won ImageNet and started the deep learning boom.
- **VGG** – deep stacks of 3×3 convolutions.
- **GoogLeNet/Inception** – parallel filters of different sizes, 1×1 convolutions for cheap channel reduction.
- **ResNet** (2015) – **residual connections** enabled 100+ layer networks.
- **MobileNet / EfficientNet** – depthwise-separable convolutions and compound scaling for efficient (mobile) models.
- **ConvNeXt** – modernised CNNs competitive with vision transformers.

</details>

### 4. How do Vision Transformers (ViT) work? CNN vs ViT?

<details>
<summary>Answer</summary>

ViT splits an image into fixed-size **patches** (e.g. 16×16), flattens and linearly embeds each patch as a token, adds position embeddings, and processes the sequence with a standard **transformer encoder**; a class token (or pooling) feeds the classifier.

| CNN | ViT |
|---|---|
| Built-in inductive biases (locality, translation equivariance) | Few inductive biases; global attention from layer 1 |
| Data-efficient on small/medium datasets | Needs large-scale pretraining (or strong augmentation/distillation, as in DeiT) |
| Efficient at high resolution | Attention cost grows quadratically with the number of patches (Swin uses windowed attention) |

At large scale, ViTs match or beat CNNs and are the backbone of most modern vision-language models.

</details>

### 5. How do you apply transfer learning to a vision problem with little data?

<details>
<summary>Answer</summary>

1. Start from a model pretrained on a large dataset (ImageNet, or a strong self-supervised/CLIP backbone).
2. Replace the classification head with one for your classes.
3. With very little data, **freeze the backbone** and train only the head; with more data, **fine-tune** the top layers or everything with a small learning rate (often lower for earlier layers).
4. Use data augmentation, early stopping, and evaluate on a held-out set from the real deployment distribution.

Also consider zero-shot or few-shot approaches with CLIP-like or vision-language models before training anything.

</details>

### 6. What data augmentation techniques are used for images?

<details>
<summary>Answer</summary>

Geometric (random crops, flips, rotations, scaling), photometric (brightness, contrast, colour jitter, blur, noise), **Cutout/Random Erasing**, **Mixup** (blend two images and labels), **CutMix** (paste patches between images), and automated policies (RandAugment, AutoAugment). Augmentations must preserve the label (don't flip digits like 6/9, or horizontally flip text). Augmentation is applied only to training data.

</details>

### 7. How does object detection work? Two-stage vs one-stage detectors?

<details>
<summary>Answer</summary>

Detection predicts **bounding boxes + class labels** for every object.
- **Two-stage** (R-CNN → Fast R-CNN → **Faster R-CNN**) – a region proposal network suggests candidate boxes, then a second stage classifies and refines them. Accurate, slower.
- **One-stage** (**YOLO**, SSD, RetinaNet) – predict boxes and classes directly from feature maps in one pass. Fast, real-time; RetinaNet introduced **focal loss** for the background-vs-object imbalance.
- **DETR** – a transformer predicts a set of boxes directly using bipartite matching, removing anchors and non-maximum suppression.

Key concepts: **anchors**, **IoU** (intersection over union), **non-maximum suppression** (remove duplicate boxes), and the metric **mAP** (mean average precision at IoU thresholds).

</details>

### 8. Semantic vs instance vs panoptic segmentation?

<details>
<summary>Answer</summary>

- **Semantic** – a class for every pixel (road, car, sky) without separating objects. Architectures: **U-Net** (encoder-decoder with skip connections, standard in medical imaging), DeepLab.
- **Instance** – separate masks for each object (car #1, car #2). **Mask R-CNN** adds a mask branch to Faster R-CNN.
- **Panoptic** – both: every pixel gets a class, and countable objects get instance IDs.
- **Segment Anything (SAM)** – a promptable foundation model that segments any object from points, boxes or text-guided prompts, enabling zero-shot segmentation and fast labelling.

</details>

### 9. What is CLIP and why was it important?

<details>
<summary>Answer</summary>

**CLIP** trains an image encoder and a text encoder together on hundreds of millions of image-caption pairs with a **contrastive loss**: matching pairs get high similarity, mismatched pairs low. The result is a **shared embedding space** for images and text, enabling:
- **Zero-shot classification** ("a photo of a {label}" prompts compared to the image embedding).
- Text-to-image and image-to-image **search**.
- Conditioning for text-to-image generation, and vision encoders for multimodal LLMs.

</details>

### 10. How do multimodal (vision-language) models work?

<details>
<summary>Answer</summary>

A typical design: a **vision encoder** (often a ViT, e.g. from CLIP/SigLIP) turns image patches into embeddings; a **projector/adapter** maps them into the LLM's token embedding space; the **LLM** then attends over image tokens and text tokens together and generates text (e.g. LLaVA-style models). Some models are trained natively multimodal from the start. Capabilities: visual question answering, OCR and document understanding, chart reading, screenshot understanding for agents. Challenges: hallucinating details not in the image, fine-grained counting and spatial reasoning, and the token cost of high-resolution images.

</details>

### 11. How do you deploy a vision model on a mobile or edge device?

<details>
<summary>Answer</summary>

- Choose an efficient architecture (MobileNet, EfficientNet-Lite, small ViTs, YOLO nano variants).
- **Quantise** (INT8) and possibly **prune** or **distil** from a larger model.
- Convert to an on-device runtime: **LiteRT (TensorFlow Lite)**, **Core ML**, ONNX Runtime, ExecuTorch; use hardware accelerators (NPU/GPU delegates).
- Lower input resolution where acceptable; batch or skip frames for video.
- Measure latency, memory, battery and accuracy on real devices, not just on a desktop GPU.

</details>

### 12. What is a 1×1 convolution used for?

<details>
<summary>Answer</summary>

It mixes information **across channels** at each pixel without looking at neighbours: effectively a per-pixel dense layer. Uses: reducing or expanding channel count cheaply (bottleneck blocks in ResNet and Inception), adding non-linearity, and as the pointwise part of depthwise-separable convolutions (MobileNet).

</details>

### 13. How do you handle class imbalance and small objects in detection?

<details>
<summary>Answer</summary>

- **Focal loss** and hard example mining for the overwhelming number of background regions.
- **Feature pyramid networks (FPN)** – detect at multiple scales so small objects are seen at high resolution.
- Higher input resolution or tiling large images (satellite, medical).
- Oversampling images containing rare classes; copy-paste augmentation.
- Tune anchor sizes (or use anchor-free detectors) to match object sizes.

</details>

### 14. What is self-supervised learning in vision (SimCLR, MAE, DINO)?

<details>
<summary>Answer</summary>

Learning visual representations from **unlabelled images** by creating a training signal from the data itself:
- **Contrastive (SimCLR, MoCo)** – two augmented views of the same image should have similar embeddings; views of different images dissimilar.
- **Masked image modelling (MAE)** – hide most image patches and train a ViT to reconstruct them.
- **Self-distillation (DINO / DINOv2)** – a student network matches a teacher's outputs on different views, with no negatives needed.
- **Image–text contrastive (CLIP)** – uses captions as weak supervision.

These backbones transfer well to downstream tasks with few labels and power image search and multimodal models.

</details>

### 15. How do face recognition and image similarity systems work (metric learning)?

<details>
<summary>Answer</summary>

Train a network to produce **embeddings** where images of the same identity/product are close and different ones are far apart:
- **Triplet loss** – for (anchor, positive, negative), make `d(a, p) + margin < d(a, n)`; needs good hard-negative mining.
- **Margin-based softmax losses** (ArcFace, CosFace) – classification over identities with an angular margin; the standard for face recognition.

At inference, compute the embedding and compare: **verification** (is this the same person? distance below a threshold) or **identification** (nearest neighbour search over a gallery with an ANN index). Concerns: bias across demographics, spoofing (liveness detection), privacy and consent, and threshold choice (false accept vs false reject rates).

</details>

---

[⬅ Previous: Neural Network Fundamentals](06-neural-networks.md) · [⬅ Back to index](../README.md) · [Next: NLP & Sequence Models ➡](08-nlp-sequence-models.md)
