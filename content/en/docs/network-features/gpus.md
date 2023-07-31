---
categories: ["Concepts"]
tags: []
title: "GPUs"
linkTitle: "GPUs"
date: 2023-04-19
---

The Akash Network is a decentralized cloud computing platform that allows users to deploy and manage containerized applications. While primarily designed for CPU-based workloads, Akash also provides support for GPU-enabled workloads, catering to the needs of various applications, such as machine learning, graphics rendering, and scientific simulations.

## Overview

We can utilize GPU resources on the Akash Network. The key aspects to consider while deploying GPU-enabled workloads on Akash are:

1. Specifying GPU requirements in the deployment configuration (e.g., GPU type and count).
2. Selecting a compatible provider that offers the necessary GPU resources.
3. Building your application using GPU-enabled Docker images with the required drivers and libraries.

## Specifying GPU Requirements
In your deployment configuration file, outline your application's GPU needs by specifying the type and quantity of GPUs required. Additionally, set any other resource constraints, such as CPU and memory, to ensure your application runs smoothly.

## Selecting a Compatible Provider
Not all providers on the Akash Network have GPU resources. Therefore, carefully choose a provider that meets your application's GPU requirements. When viewing bids for your deployment, filter the list of providers based on their GPU offerings to find a suitable match.

## Use GPU-enabled Docker images
To take full advantage of the GPU capabilities offered by your chosen provider, build your application using a GPU-enabled Docker image. These images come pre-configured with the necessary drivers and libraries, such as CUDA and cuDNN, enabling your application to run GPU-accelerated workloads effectively.

By paying close attention to these three aspects, you can efficiently deploy and run GPU-enabled workloads on the Akash Network, harnessing the power of decentralized cloud computing for a broad array of GPU-intensive applications.