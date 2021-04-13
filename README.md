# IAug_CDNet
**Official Implementation of Adversarial Instance Augmentation for Building Change Detection in Remote Sensing Images.**

## Overview

We propose a novel data-level solution, namely Instance-level change Augmentation (IAug), to generate bi-temporal images that contain changes involving plenty and diverse buildings by leveraging generative adversarial training. The key of IAug is to blend synthesized building instances onto appropriate positions of one of the bi-temporal images. To achieve this, a building generator is employed to produce realistic building images that are consistent with the given layouts. Diverse styles are later transferred onto the generated images. We further propose context-aware blending for a realistic composite of the building and the background. We augment the existing CD datasets and also design a simple yet effective CD model - CDNet. Our method (CDNet + IAug) has achieved state-of-the-art results in two building CD datasets (LEVIR-CD and WHU-CD). Interestingly, we achieve comparable results with only 20\% of the training data as the current state-of-the-art methods using 100\% data. Extensive experiments have validated the effectiveness of the proposed IAug. Our augmented dataset has a lower risk of class imbalance than the original one. Conventional learning on the synthesized dataset outperforms several popular cost-sensitive algorithms on the original dataset.

![](./images/example.png)

![](./images/overall-method.png)

# Building Generator

See [building generator](building_generator/README.md) for details.

Synthesized images (256 * 256) by the generator (trained on the AIRS building dataset).![syn_example_airs](./images/syn_example_airs.png)

Synthesized images (64 * 64) by the generator (trained on the Inria building dataset).![syn_example_inria](./images/syn_example_inria.png)

## Installation

This code requires PyTorch 1.0 and python 3+. Please install dependencies by

```bash
pip install -r requirements.txt
```

## Generating Images Using Pretrained Model

Once the dataset is ready, the result images can be generated using pretrained models.

1. Download the tar of the pretrained models from the Google Drive

   - Inria_pretrained: [net_G](https://drive.google.com/file/d/1YwwX7IwxSGR5551OIKRicemQpAxRJuCL/view?usp=sharing), [net_D](https://drive.google.com/file/d/1DIHLV_e7nd4kjDVnILg5mqjR9gxnJmEZ/view?usp=sharing) 
   - AIRS pretrained: [net_G](https://drive.google.com/file/d/1eGMwYKaaKvNellBrTV51jfeJZuPVIjm3/view?usp=sharing), [net_D](https://drive.google.com/file/d/1q5-GJcYoB7niVdR2YEftdi0NP2L2Fo0w/view?usp=sharing)
   - save it in 'checkpoints/', and run

2. Generate images using the pretrained model.

   ```bash
   python test.py --model pix2pix --name $pretrained_folder --results_dir $results_dir --dataset_mode custom --label_dir $label_dir --label_nc 2 --batchSize $batchSize --load_size $size --crop_size $size --no_instance --which_epoch lastest
   ```

   `pretrained_folder` is the directory name of the checkpoint file downloaded in Step 1, `results_dir` is the directory name to save the synthesized images, `label_dir` is the directory name of the semantic labels, `size` is the size of the label map fed to the generator.  

3. The outputs images are stored at `results_dir`. You can view them using the autogenerated HTML file in the directory.

For simplicity, we also provide the test script in `scripts/run_test.sh`, one can modify the `label_dir` and `name` and then run the script.

## Training New Models

New models can be trained with the following commands.

1. Prepare the dataset. You can first prepare the building image patches and corresponding label maps in two folders (`image_dir`, `label_dir`).

2. Train the model.

```bash
# To train on your own custom dataset
python train.py --name [experiment_name] --dataset_mode custom --label_dir [label_dir] -- image_dir [image_dir] --label_nc 2
```

There are many options you can specify. Please use `python train.py --help`. The specified options are printed to the console. To specify the number of GPUs to utilize, use `--gpu_ids`. If you want to use the second and third GPUs for example, use `--gpu_ids 1,2`.

To log training, use `--tf_log` for Tensorboard. The logs are stored at `[checkpoints_dir]/[name]/logs`.

## Acknowledge

This code borrows heavily from [spade](https://github.com/nvlabs/spade/).

# Color Transfer

See [Color Transfer](./ColorTransfer/README.md) for deteils.

We resort to a simple yet effective nonlearning approach to match the color distribution of the two image sets (GAN-generated images and original images in the change detection dataset).

![color_transfer](./images/color_transfer.png)

## Requirements

- Matlab

## Usage

We provide two demos to show the color transfer. 

When you do not have the object mask. You can edit the file `ColorTransferDemo.m`, modify the file path of the `Im_target` and `Im_source`. After you run this file, the transfered image is saved as `result_Image.jpg`.

When you do have both the building image and the object mask. You can edit the file `ColorTransferDemo_with_mask.m`, modify the file path of the `Im_target`, `Im_source`, `m_target` and `m_source`. After you run this file, the transfered image is saved as `result_Image.jpg`.

## Acknowledge 

This code borrows heavily from https://github.com/AissamDjahnine/ColorTransfer. 

# Shadow Extraction

We show a simple shadow extraction method. The extracted shadow information can be used to make a more realistic image composite in the latter process. 

![shadow_extraction](/images/shadow_extraction.png)

We provide some examples for shadow extraction. The samples are in the folder `samples\shadow_sample`.

## Usage

You can edit the file `extract_shadow.py` and modify the path of the `image_folder`, `label_folder` and `out_folder`. Make sure that the image files are in `image_folder` and the corresponding label files are in `label_folder`. Run the following script:

```bash
python extract_shadow.py
```

Once you have successfully run the python file, the results can be found in the `out folder`.

# Instance augmentation

Here, we provide the python implementation of instance augmentation. 

![image-20210413152845314](./images/synthesized_CD_sample.png)

We provide some examples for instance augmentation. The samples are in the folder `samples\SYN_CD`.

## Usage

You can edit the file `composite_CD_sample.py` and modify the following values: 

```
#  first define the some paths
A_folder = r'samples\LEVIR\A'
B_folder = r'samples\LEVIR\B'
L_folder = r'samples\LEVIR\label'
ref_folder = r'samples\LEVIR\ref'
#  instance path
src_folder = r'samples\SYN_CD\image' #test
label_folder = r'samples\SYN_CD\shadow'  # test
out_folder = r'samples\SYN_CD\out_sample'
os.makedirs(out_folder, exist_ok=True)
# how many instance to paste per sample
M = 50
```

 Then, run the following script:

```bash
python composite_CD_sample.py
```

Once you have successfully run the python file, the results can be found in the `out folder`.

# CDNet

Coming soon~~~~



## Citation

If you use this code for your research, please cite our paper:

```
@Article{chen2021,
    title={Adversarial Instance Augmentation for Building Change Detection in Remote Sensing Images},
    author={Hao Chen, Wenyuan Li and Zhenwei Shi},
    year={2021},
    journal={IEEE Transactions on Geoscience and Remote Sensing},
    volume={},
    number={},
    pages={1-16},
    doi={10.1109/TGRS.2021.3066802}
}
```

