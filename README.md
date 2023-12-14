# Instruct-GS2GS: Editing 3D Scenes with Instructions
Cyrus Vachha and Ayaan Haque

Code: [https://github.com/cvachha/instruct-gs2gs](https://github.com/cvachha/instruct-gs2gs)

<video src="https://github.com/cvachha/instruct-gs2gs/assets/9502341/3009a2e6-be94-44da-b61e-6640dbc2039a" muted autoplay controls playsinline loop type="video/mp4" height="500">
</video>


We propose a method for editing 3D Gaussian Splatting scenes with text-instructions. Our work is based largely off Instruct-NeRF2NeRF which proposes an iterative dataset update method to make consistent 3D  edits to Neural Radiance Fields given a text instruction. We propose a modified technique to adapt the editing scheme for 3D gaussian splatting scenes. We demonstrate comparable results to Instruct-NeRF2NeRF and show that our method can perform realistic global text edits on large real-world scenes and individual subjects.

## Introduction
Recent advances in photo-realistic novel 3D representations such as Neural Radiance Fields (NeRF) and 3D Gaussian Splatting (3DGS) have instigated a multitude of works exploring 3D generation, neural 3D reconstruction, and practical applications for these representations. Editing novel 3D representations like NeRF or 3DGS remains a challenge, and traditional 3D tools are generally incompatible with these representations. Instruct-NeRF2NeRF describes a method to semantically edit NeRFs with text instructions. Instruct-NeRF2NeRF uses a 2D diffusion model, Instruct-Pix2Pix, to iteratively edit the training dataset and update the NeRF simultaneously. Recently, 3DGS has gained popularity as a representation, but the Instruct-NeRF2NeRF algorithm cannot be naively applied to gaussian splatting. While NeRFs offer detailed 3D reconstructions, 3DGS has the primary advantage of real-time rendering speeds, making it a more suitable choice for integration with game engines, web compatibility, and virtual reality.

In this paper, we propose Instruct-GS2GS, a method to edit 3D Gaussian Splatting scenes and objects with global text instructions. Our method performs edits on a pre-captured 3DGS scene in a 3D consistent manner, similar to Instruct-NeRF2NeRF, while also having much a faster training and inference speed. Our method adapts the iterative dataset update approach from Instruct-NeRF2NeRF to work effectively for 3DGS. Our method is implemented in Nerfstudio, allowing users to perform edits quickly and view them in real-time.

## Method

Our method takes in a dataset of camera poses and training images, a trained 3DGS scene, and a user specified text-prompt instruction, e.g. "make him a marble statue". Instruct-GS2GS constructs the edited GS scene guided by the text-prompt by applying a 2D text and image conditioned diffusion model, in this case Instruct-Pix2Pix, to all training images over the course of training. We perform these edits using an iterative udpate scheme in which all training dataset images are updated using a diffusion model individually, for sequential iterations spanning the size of the training images, every 2.5k training iterations. This process allows the GS to have a holistic edit and maintain 3D consistency.

![pipeline](webpage_imgs\igs2gs_pipeline.png)

Our process is similar to Instruct-NeRF2NeRF where for a given training camera view, we set the original training image as the conditioning image, the noisy image input as the NeRF rendered from the camera combined with some randomly selected noise, and receive an edited image respecting the text conditioning. With this method we are able to propagate the edited changes to the GS scene. We are able to maintain grounded edits by conditioning Instruct-Pix2Pix on the original unedited training image.

### Implementation
We use Nerfstudio's gsplat library for our underlying gaussian splatting model. We adapt similar parameters for the diffusion model from Instruct-NeRF2NeRF. Among these are the values for $[t_\text{min}, t_\text{max}] = [0.70,0.98]$, which define the amount of noise (and therefore the amount signal retained from the original images). We vary the classifier-free guidance scales per edit and scene, using a range of values from $s_I=(1.2,1.5)$ and $s_T=(7.5,12.5)$. We edit the entire dataset and then train the scene for 2.5k iterations. For GS training, we use L1 and LPIPS losses. We train our method for a maximum of 30k iterations (starting with a GS scene trained for 20k iterations). However, in practice we stop training once the edit has converged. In many cases, the optimal training length is a subjective decision — a user may prefer more subtle or more extreme edits that are best found at different stages of training. 

# Results
Our qualitative results are shown in our first video and the following results. For each edit, we show multiple views to illustrate the 3D consistency. On the portrait capture in the first video, we are able to perform the same edits as Instruct-NeRF2NeRF, as well as new edits like "turn him into a Lego Man." In certain cases, the results look more 3D consistent and higher quality, and we provide a comparison below. However, the gaussian splatting representation makes it challenging to add entirely new geometry. These edits also extend to subjects other than people, like changing a bear statue into a real polar bear, panda, and grizzly bear. We are able to edit large-scale scenes just like Instruct-NeRF2NeRF, while maintaining the same level of 3D consistency. 

![timelapse](webpage_imgs\comparison_igs2gs.png)

Most importantly, we find that our method outputs a reasonable result in around 13 min while Instruct-NeRF2NeRF takes approximately 50 min on the same scene.

<video src="https://github.com/cvachha/instruct-gs2gs/assets/9502341/bb737928-1e5b-4ee3-9e92-48429d6eb4a8" muted autoplay controls playsinline loop type="video/mp4" height="300">
</video>


Below we show results on real-world environments 
<video src="https://github.com/cvachha/instruct-gs2gs/assets/9502341/98927461-8f05-43a9-a469-ed45d5bf5eb5" muted autoplay controls playsinline loop type="video/mp4" height="450">
</video>

<video src="https://github.com/cvachha/instruct-gs2gs/assets/9502341/f21c7163-1202-43a2-8b94-3b7869a5b3bb" muted autoplay controls playsinline loop type="video/mp4"height="450">
</video>

<video src="https://github.com/cvachha/instruct-gs2gs/assets/9502341/a62aaa4d-a212-4033-8fab-9dca437f4a8e" muted autoplay controls playsinline loop type="video/mp4" height="450">
</video>

<video src="https://github.com/cvachha/instruct-gs2gs/assets/9502341/09ce2c2e-80c0-4971-9900-179b94eb4705" muted autoplay controls playsinline loop type="video/mp4" height="450">
</video>

### Coming soon to Nerfstudio!
Repo: [https://github.com/cvachha/instruct-gs2gs](https://github.com/cvachha/instruct-gs2gs)

![nerfstudio_igs2gs](webpage_imgs\nerfstudio_igs2gs.png)


## Acknowledgements 

We thank our instructors Alexei A. Efros and Angjoo Kanazawa for their support on this project. We would also like to thank the Nerfstudio and gsplat team for providing the 3D Gaussian Splatting implementation.