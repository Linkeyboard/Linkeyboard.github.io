---
title: (J0034)CNN-based driving of block partitioning for intra slices encoding
date: 2018-07-22 21:58:23
tags: JVET
categories: paper reading
---
### Abstract
A CNN-based ending approach is explored to partly substitude deuristics-based encoder.In intra slices, this solution allows controlling the trade-off between BD-rate gain and speed-up with one parameter.
### Details
- This solution drives the endoer by estimating the probabilites of blocks splitting in intra slices.This approach based on the texture analysis of original blocks and replaces the RDO.
- A convolutional Neural Network based analysis is prosed to speed-up the intra slice s encoding process.The methos is based on texture analysis of original 64x64 root blocks, luma or chroma. This method aims at enabling the encoder to leverage the gains of BT and ABT partitioning, while reduing the combinatory for the RDO checks

