---
layout: post

---

Useful links:

- [Attention Is All You Need](https://arxiv.org/pdf/1706.03762.pdf).
- [Transformer lecture video](https://www.youtube.com/watch?v=ugWDIIOHtPA&t=2401s) by [Hung-yi Lee](https://www.youtube.com/channel/UC2ggjtuuWvxrHHHiaDH1dlQ)

### 1. Introduction

Recurrent models 的特征：

- factor computation along the symbol positions
- align the positions to steps in computation time ($h_{t-1}\rightarrow h_t$)
- preclude parallelization within training examples (sequential computation)

Attention mechanism 的特征：

- allow modeling of dependencies without regard to their distance in the input or output sequences

### 2. Background

