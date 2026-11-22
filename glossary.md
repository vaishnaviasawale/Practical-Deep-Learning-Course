# Glossary

## Convolutional neural network (CNN)

A **convolutional neural network** is a neural network that processes grid-structured data such as images. Instead of connecting every input pixel to every neuron, it uses small learnable filters (kernels), for example 3×3 grids of weights, that slide across the image. The same filter is reused at every position, so the network learns to detect a pattern wherever it appears.

Key ideas:

- **Convolution layers** apply the filters to produce feature maps. Early layers typically detect simple features such as edges and colours; deeper layers combine them into textures, parts, and objects.
- **Pooling layers** downsample the feature maps, shrinking the spatial size and making the network less sensitive to exact positions.
- The final layers usually turn the learned features into predictions, such as class probabilities.

## Gradient

A **gradient** is the vector of partial derivatives of the loss (the measure of how wrong the model is) with respect to each of the model's parameters (weights). It tells you how much a small change in each weight would change the loss, and in which direction.

Training uses gradients to improve the model. **Gradient descent** nudges every weight a small step in the direction that reduces the loss, and repeats this over many batches. The step size is set by the **learning rate**. Gradients are computed efficiently for deep networks by **backpropagation**, which applies the chain rule layer by layer from the output back to the input.

Gradients can become very small (**vanishing gradients**) or very large (**exploding gradients**) as they pass through many layers, which makes deep networks hard to train.

## Residual connection (skip connection)

A **residual connection**, also called a **skip connection**, adds a layer's input directly to its output, bypassing the layers in between. If a block computes a function `F(x)`, a residual block outputs `F(x) + x` instead of `F(x)` alone. The block then only needs to learn the *residual*, the difference from the identity.

Why it matters: during backpropagation, the skip path gives gradients a direct route back to earlier layers, so they do not shrink to nearly zero as they travel through many layers. This is what makes very deep networks trainable. Skip connections are used in ResNet and, in a different form (concatenation), in U-Net.

## ResNet

**ResNet** (Residual Network) is a family of convolutional neural networks introduced by Microsoft Research in 2015. Its key idea is the **residual connection** (or skip connection): each block learns a residual function `F(x)` and outputs `F(x) + x`, so the input is added back to the block's output.

Why it matters:

- Very deep networks (50, 101, 152+ layers) were hard to train before ResNet because gradients vanish or degrade as depth grows. Skip connections give gradients a direct path backward, which makes deep networks trainable.
- Variants are named by depth, e.g. `resnet18`, `resnet34`, `resnet50`. Larger numbers are deeper and usually more accurate, but slower and more memory-hungry.
- ResNets pretrained on ImageNet are the usual starting point for image classification, and are the default backbone for transfer learning in this course.

## Epoch

An **epoch** is one complete pass through the entire training dataset. If you have 1,000 images and a batch size of 100, one epoch is 10 batches (10 gradient updates).

Notes:

- Training usually runs for many epochs, so the model sees each example repeatedly.
- Too few epochs leaves the model underfit; too many can lead to **overfitting**, where training accuracy keeps improving but validation accuracy stops improving or gets worse.
- Metrics such as training loss and validation loss are typically reported once per epoch.

## Fine-tuning

**Fine-tuning** means taking a model that has already been trained (usually on a large dataset such as ImageNet) and continuing to train it on your own, usually smaller, dataset. It is a form of **transfer learning**.

Typical approach:

1. Start from pretrained weights. The early layers have learned general features (edges, textures, shapes), which are reusable.
2. Replace the final layer(s) so the output matches your classes.
3. Train. Either update all layers with a small learning rate, or freeze the early layers and train only the head first.

Why it matters: fine-tuning needs far less data and compute than training from scratch, and usually gives better results on small datasets.

## U-Net architecture

**U-Net** is a convolutional neural network originally designed for biomedical image segmentation (Ronneberger et al., 2015). It is named after its U-shaped diagram.

It has two paths:

- **Encoder (contracting path):** a series of convolution and pooling steps that reduce spatial resolution while increasing the number of feature channels. This captures *what* is in the image.
- **Decoder (expanding path):** a series of upsampling and convolution steps that restore spatial resolution. This recovers *where* things are, so the output is a per-pixel prediction.
- **Skip connections:** at each resolution level, the encoder's feature maps are concatenated into the matching decoder layer. These pass fine spatial detail from the encoder directly to the decoder, which is what makes precise, pixel-level boundaries possible.

Typical uses: semantic segmentation (labelling every pixel with a class), medical imaging, and image-to-image tasks such as denoising or colorization.
