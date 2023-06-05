# ComfyUI-Impact-Pack

This custom node helps to conveniently enhance images through Detector, Detailer, Upscaler, Pipe, and more.

## Custom nodes pack for ComfyUI

# Custom Nodes
* SAMLoader - Load SAM model
* MMDetDetectorProvider - Load MMDet model to provide BBOX_DETECTOR, SEGM_DETECTOR
* ONNXDetectorProvider - Load ONNX model to provide SEGM_DETECTOR
* CLIPSegDetectorProvider - CLIPSeg wrapper to provide BBOX_DETECTOR
  * You need to install the [ComfyUI-CLIPSeg](https://github.com/biegert/ComfyUI-CLIPSeg) node extension. 
* SEGM Detector (combined) - Detect segmentation and return mask from input image.
* BBOX Detector (combined) - Detect bbox(bounding box) and return mask from input image.
* SAMDetector (combined) - Using the technology of SAM, extract the segment at the location indicated by the input SEGS on the input image, and output it as a unified mask. 
* Bitwise(SEGS & SEGS) - Perform 'bitwise and' operations between 2 SEGS.
* Bitwise(SEGS - SEGS) - Perform subtract operations between 2 SEGS.
* Bitwise(SEGS & MASK) - Perform a bitwise AND operation on SEGS and MASK.
* Bitwise(MASK & MASK) - Perform 'bitwise and' operations between 2 masks
* Bitwise(MASK - MASK) - Perform subtract operations between 2 masks
* SEGM Detector (SEGS) - Detect segmentation and return SEGS from input image.
* BBOX Detector (SEGS) - Detect bbox(bounding box) and return SEGS from input image.
* ONNX Detector (SEGS) - Using the ONNX model, identify the bbox and retrieve the SEGS from the input image
* Detailer (SEGS) - Refine image rely on SEGS.
* DetailerDebug (SEGS) - Refine image rely on SEGS. Additionally, you can monitor cropped image and refined image of cropped image.
   * To prevent the regeneration caused by the seed that does not change every time when using 'external_seed', please disable the 'seed random generate' option in the 'Detailer...' node
* MASK to SEGS - This node generates SEGS based on the mask. 
* ToBinaryMask - This node separates the mask generated with alpha values between 0 and 255 into 0 and 255. The non-zero parts are always set to 255.
* EmptySEGS - This node provides a empty SEGS.
* MaskPainter - This node provides a feature to draw masks.
* FaceDetailer - This is a node that can easily detect faces and improve them.
* FaceDetailer (pipe) - This is a node that can easily detect faces and improve them. (for multipass)
* Pipe nodes
   * ToDetailerPipe, FromDetailerPipe - These nodes are used to bundle multiple inputs used in the detailer, such as models and vae, ..., into a single DETAILER_PIPE or extract the elements that are bundled in the DETAILER_PIPE.
   * ToBasicPipe, FromBasicPipe - These nodes are used to bundle model, clip, vae, positive conditioning, and negative conditioning into a single BASIC_PIPE, or extract each element from the BASIC_PIPE.
   * EditBasicPipe, EditDetailerPipe - These nodes are used to replace some elements in BASIC_PIPE or DETAILER_PIPE.
* Latent Scale (on Pixel Space) - This node converts latent to pixel space, upscales it, and then converts it back to latent.
   * If upscale_model_opt is provided, it uses the model to upscale the pixel and then downscales it using the interpolation method provided in scale_method to the target resolution.
* PixelKSampleUpscalerProvider - An upscaler is provided that converts latent to pixels using VAEDecode, performs upscaling, converts back to latent using VAEEncode, and then performs k-sampling. This upscaler can be attached to nodes such as 'Iterative Upscale' for use.
  * Similar to 'Latent Scale (on Pixel Space)', if upscale_model_opt is provided, it performs pixel upscaling using the model.
* PixelTiledKSampleUpscalerProvider - It is similar to PixelKSampleUpscalerProvider, but it uses ComfyUI_TiledKSampler and Tiled VAE Decoder/Encoder to avoid GPU VRAM issues at high resolutions.
  * You need to install the [ComfyUI_TiledKSampler](https://github.com/BlenderNeko/ComfyUI_TiledKSampler) node extension.

* DenoiseScheduleHookProvider - IterativeUpscale provides a hook that gradually changes the denoise to target_denoise as the step progresses.
* CfgScheduleHookProvider - IterativeUpscale provides a hook that gradually changes the cfg to target_cfg as the step progresses.
* PixelKSampleHookCombine - This is used to connect two PK_HOOKs. hook1 is executed first and then hook2 is executed.
  * If you want to simultaneously change cfg and denoise, you can combine the PK_HOOKs of CfgScheduleHookProvider and PixelKSampleHookCombine.

* Iterative Upscale (Latent) - The upscaler takes the input upscaler and splits the scale_factor into steps, then iteratively performs upscaling. 
This takes latent as input and outputs latent as the result.
* Iterative Upscale (Image) - The upscaler takes the input upscaler and splits the scale_factor into steps, then iteratively performs upscaling. This takes image as input and outputs image as the result.
  * Internally, this node uses 'Iterative Upscale (Latent)'.

* TwoSamplersForMask - This node can apply two samplers depending on the mask area. The base_sampler is applied to the area where the mask is 0, while the mask_sampler is applied to the area where the mask is 1.
  * Note: The latent encoded through VAEEncodeForInpaint cannot be used.
* KSamplerProvider - This is a wrapper that enables KSampler to be used in TwoSamplersForMask TwoSamplersForMaskUpscalerProvider.
* TiledKSamplerProvider - ComfyUI_TiledKSampler is a wrapper that provides KSAMPLER.
  * You need to install the [ComfyUI_TiledKSampler](https://github.com/BlenderNeko/ComfyUI_TiledKSampler) node extension.
  
* TwoSamplersForMaskUpscalerProvider - This is an Upscaler that extends TwoSamplersForMask to be used in Iterative Upscale.
  * TwoSamplersForMaskUpscalerProviderPipe - pipe version of TwoSamplersForMaskUpscalerProvider.

* PreviewBridge - This custom node can be used with a bridge when using the MaskEditor feature of Clipspace.

# Feature
* Interactive SAM Detector (Clipspace) - When you right-click on a node that has 'MASK' and 'IMAGE' outputs, a context menu will open. From this menu, you can either open a dialog to create a SAM Mask using 'Open in SAM Detector', or copy the content (likely mask data) using 'Copy (Clipspace)' and generate a mask using 'Impact SAM Detector' from the clipspace menu, and then paste it using 'Paste (Clipspace)'.


# Deprecated
* The following nodes have been kept only for compatibility with existing workflows, and are no longer supported. Please replace them with new nodes.
   * MMDetLoader -> MMDetDetectorProvider
   * SegsMaskCombine -> SEGS to MASK (combined)
   * BboxDetectorForEach -> BBOX Detector (SEGS)
   * SegmDetectorForEach -> SEGM Detector (SEGS)
   * BboxDetectorCombined -> BBOX Detector (combined)
   * SegmDetectorCombined -> SEGM Detector (combined)
   * MaskPainter -> PreviewBridge

# Installation

1. cd custom_nodes
1. git clone https://github.com/ltdrdata/ComfyUI-Impact-Pack.git
3. cd ComfyUI-Impact-Pack
4. (optional) python install.py
   * Impact Pack will automatically install its dependencies during its initial launch.
5. Restart ComfyUI

* You can use this colab notebook [colab notebook](https://colab.research.google.com/github/ltdrdata/ComfyUI-Impact-Pack/blob/Main/notebook/comfyui_colab_impact_pack.ipynb) to launch it. This notebook automatically downloads the impact pack to the custom_nodes directory, installs the tested dependencies, and runs it.

# Package Dependencies (If you need to manual setup.)

* pip install
   * openmim
   * segment-anything
   * pycocotools
   * onnxruntime
   
* mim install
   * mmcv==2.0.0, mmdet==3.0.0, mmengine==0.7.2

* linux packages (ubuntu)
  * libgl1-mesa-glx
  * libglib2.0-0

# Other Materials (auto-download on initial startup)

* ComfyUI/models/mmdets/bbox <= https://huggingface.co/dustysys/ddetailer/resolve/main/mmdet/bbox/mmdet_anime-face_yolov3.pth
* ComfyUI/models/mmdets/bbox <= https://raw.githubusercontent.com/Bing-su/dddetailer/master/config/mmdet_anime-face_yolov3.py
* ComfyUI/models/sams <= https://dl.fbaipublicfiles.com/segment_anything/sam_vit_b_01ec64.pth

# Troubleshooting page
* [Troubleshooting Page](troubleshooting/TROUBLESHOOTING.md)


# How to use (DDetailer feature)

#### 1. Basic auto face detection and refine example.
![simple](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/simple.png)
* The face that has been damaged due to low resolution is restored with high resolution by generating and synthesizing it, in order to restore the details.
* The FaceDetailer node is a combination of a Detector node for face detection and a Detailer node for image enhancement. See the [Advanced Tutorial](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/tutorial/advanced.md) for a more detailed explanation.
* Pass the MMDetLoader 's bbox model and the detection model loaded by SAMLoader to FaceDetailer . Since it performs the function of KSampler for image enhancement, it overlaps with KSampler's options.
* The MASK output of FaceDetailer provides a visualization of where the detected and enhanced areas are.

![simple-orig](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/simple-original.png) ![simple-refined](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/simple-refined.png)
* You can see that the face in the image on the left has increased detail as in the image on the right.

#### 2. 2Pass refine (restore a severely damaged face)
![2pass-workflow-example](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/2pass-simple.png)
* Although two FaceDetailers can be attached together for a 2-pass configuration, various common inputs used in KSampler can be passed through DETAILER_PIPE, so FaceDetailerPipe can be used to configure easily.
* In 1pass, only rough outline recovery is required, so restore with a reasonable resolution and low options. However, if you increase the dilation at this time, not only the face but also the surrounding parts are included in the recovery range, so it is useful when you need to reshape the face other than the facial part.

![2pass-example-original](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/2pass-original.png) ![2pass-example-middle](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/2pass-1pass.png) ![2pass-example-result](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/2pass-2pass.png)
* In the first stage, the severely damaged face is restored to some extent, and in the second stage, the details are restored

#### 3. Face Bbox(bounding box) + Person silhouette segmentation (prevent distortion of the background.)
![combination-workflow-example](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/combination.png)
![combination-example-original](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/combination-original.png) ![combination-example-refined](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/combination-refined.png)

* Facial synthesis that emphasizes details is delicately aligned with the contours of the face, and it can be observed that it does not affect the image outside of the face.

* The BBoxDetectorForEach node is used to detect faces, and the SAMDetectorCombined node is used to find the segment related to the detected face. By using the Segs & Mask node with the two masks obtained in this way, an accurate mask that intersects based on segs can be generated. If this generated mask is input to the DetailerForEach node, only the target area can be created in high resolution from the image and then composited.

#### 4. Iterative Upscale
![upscale-workflow-example](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/upscale-workflow.png)
 
* The IterativeUpscale node is a node that enlarges an image/latent by a scale_factor. In this process, the upscale is carried out progressively by dividing it into steps.
* IterativeUpscale takes an Upscaler as an input, similar to a plugin, and uses it during each iteration. PixelKSampleUpscalerProvider is an Upscaler that converts the latent representation to pixel space and applies ksampling.
  * The upscale_model_opt is an optional parameter that determines whether to use the upscale function of the model base if available. Using the upscale function of the model base can significantly reduce the number of iterative steps required. If an x2 upscaler is used, the image/latent is first upscaled by a factor of 2 and then downscaled to the target scale at each step before further processing is done.

* The following image is an image of 304x512 pixels and the same image scaled up to three times its original size using IterativeUpscale.

![combination-example-original](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/upscale-original.png) ![combination-example-refined](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/upscale-3x.png)


#### 5. Interactive SAM Detector (Clipspace)

* When you right-click on the node that outputs 'MASK' and 'IMAGE', a menu called "Open in SAM Detector" appears, as shown in the following picture. Clicking on the menu opens a dialog in SAM's functionality, allowing you to generate a segment mask.
![samdetector-menu](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/SAMDetector-menu.png)

* By clicking the left mouse button on a coordinate, a positive prompt in blue color is entered, indicating the area that should be included. Clicking the right mouse button on a coordinate enters a negative prompt in red color, indicating the area that should be excluded. Positive prompts represent the areas that should be included, while negative prompts represent the areas that should be excluded.
* You can remove the points that were added by using the "undo" button. After selecting the points, pressing the "detect" button generates the mask. Additionally, you can adjust the fidelity slider to determine the extent to which the mask belongs to the confidence region.

![samdetector-dialog](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/SAMDetector-dialog.png)

* If you opened the dialog through "Open in SAM Detector" from the node, you can directly apply the changes by clicking the "Save to node" button. However, if you opened the dialog through the "clipspace" menu, you can save it to clipspace by clicking the "Save" button.

![samdetector-result](https://github.com/ltdrdata/ComfyUI-extension-tutorials/raw/Main/ComfyUI-Impact-Pack/images/SAMDetector-result.png)

* When you execute using the reflected mask in the node, you can observe that the image and mask are displayed separately.

# Others Tutorials
* [ComfyUI-extension-tutorials/ComfyUI-Impact-Pack](https://github.com/ltdrdata/ComfyUI-extension-tutorials/tree/Main/ComfyUI-Impact-Pack) - You can find various tutorials and workflows on this page.
* [Advanced Tutorial](https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/advanced.md)
* [SAM Application](https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/sam.md)
* [PreviewBridge](https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/previewbridge.md)
* [Mask Pointer](https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/maskpointer.md)
* [ONNX Tutorial](https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/ONNX.md)
* [CLIPSeg Tutorial](https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/clipseg.md)
* [Extreme Highresolution Upscale](https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/extreme-upscale.md)
* [TwoSamplersForMask](https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/TwoSamplers.md)
* [Advanced Iterative Upscale: PK_HOOK](https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/pk_hook.md)
* [Advanced Iterative Upscale: TwoSamplersForMask Upscale Provider](https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/TwoSamplersUpscale.md)

* [Interactive SAM + PreviewBridge](https://github.com/ltdrdata/ComfyUI-extension-tutorials/blob/Main/ComfyUI-Impact-Pack/tutorial/sam_with_preview_bridge.md)

# Credits

ComfyUI/[ComfyUI](https://github.com/comfyanonymous/ComfyUI) - A powerful and modular stable diffusion GUI.

dustysys/[ddetailer](https://github.com/dustysys/ddetailer) - DDetailer for Stable-diffusion-webUI extension.

Bing-su/[dddetailer](https://github.com/Bing-su/dddetailer) - The anime-face-detector used in ddetailer has been updated to be compatible with mmdet 3.0.0, and we have also applied a patch to the pycocotools dependency for Windows environment in ddetailer.

facebook/[segment-anything](https://github.com/facebookresearch/segment-anything) - Segmentation Anything!

hysts/[anime-face-detector](https://github.com/hysts/anime-face-detector) - Creator of `anime-face_yolov3`, which has impressive performance on a variety of art styles.

open-mmlab/[mmdetection](https://github.com/open-mmlab/mmdetection) - Object detection toolset. `dd-person_mask2former` was trained via transfer learning using their [R-50 Mask2Former instance segmentation model](https://github.com/open-mmlab/mmdetection/tree/master/configs/mask2former#instance-segmentation) as a base.

biegert/[ComfyUI-CLIPSeg](https://github.com/biegert/ComfyUI-CLIPSeg) - This is a custom node that enables the use of CLIPSeg technology, which can find segments through prompts, in ComfyUI.

BlenderNeok/[ComfyUI-TiledKSampler](https://github.com/BlenderNeko/ComfyUI_TiledKSampler) - 
The tile sampler allows high-resolution sampling even in places with low GPU VRAM.

WASasquatch/[was-node-suite-comfyui](https://github.com/WASasquatch/was-node-suite-comfyui) - A powerful custom node extensions of ComfyUI.
