Cortex

A DCGAN (Deep Convolutional Generative Adversarial Network) that learns to generate synthetic chest X-ray images. Built as a hackathon project to explore generative modeling for medical imaging data augmentation.

Overview

Cortex trains a generator/discriminator pair on the Chest X-Ray Images (Pneumonia) dataset to synthesize realistic-looking chest X-ray images from random noise. Once trained, the generator can be used standalone to produce new synthetic images.

Architecture
Generator — takes a 256-dim latent vector, projects it through a linear layer, then upsamples through transposed convolutions (with batch norm + LeakyReLU) to a 256x256x3 RGB image, squashed to [-1, 1] with Tanh.
Discriminator — a convolutional classifier with four downsampling blocks (Conv2d + LeakyReLU + Dropout, batch norm on inner layers) that outputs a single real/fake logit.
Loss — BCEWithLogitsLoss (standard adversarial loss) for both networks.
Optimizer — Adam (lr=1e-4, betas=(0.5, 0.999)) for both generator and discriminator.
Project Structure
cortex/
├── cortex.py     # Training script — builds and trains the GAN
├── test.py       # Inference script — loads a trained generator and samples images
├── .gitignore
└── README.md
Requirements
Python 3.8+
PyTorch & torchvision
tqdm
bash
pip install torch torchvision tqdm

GPU strongly recommended — training runs significantly faster with CUDA available (the scripts auto-detect and use it if present).

Dataset Setup
Download the chest X-ray dataset (e.g. from Kaggle).
Update the DATA_DIR path at the top of cortex.py to point to your local train folder, which should contain class subfolders (ImageFolder format), e.g.:
   chest_xray/train/
   ├── NORMAL/
   └── PNEUMONIA/
Usage
Training
bash
python cortex.py
Trains for 100 epochs at a batch size of 8, on 256x256 images.
Every 200 batches, a 5x5 grid of generated samples is saved to images/.
Checkpoints (generator.pth, discriminator.pth) are saved after training completes, and are automatically reloaded on the next run if present — so you can stop and resume training.
Generating Images

Once you have a trained generator.pth in the working directory:

bash
python test.py
Loads the trained generator and samples new images (defaults to 5 images, batch size 16).
Output is saved to generated_images/.
Adjust the num_images / batch_size arguments in the generate_and_save_images() call at the bottom of test.py to generate more.
Notes
DATA_DIR in cortex.py is currently a hardcoded local path — update it before running, or refactor to an argparse flag / environment variable if you plan to share this across machines.
This is a research/experimentation project — generated images are synthetic and not intended for clinical or diagnostic use.
