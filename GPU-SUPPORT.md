# GPU Support in Livepeer

Livepeer enables node operators to transcode video on GPUs while concurrently mining cryptocurrencies and performing other CUDA operations such as machine learning. As there is a very wide range of GPU hardware out there, this document aims to crowdsource a list of specific GPU models that have been tested and verified to work on Livepeer. If you've tested an additional model, please submit an update to this document.

**Overview**

* Livepeer supports transcoding on NVIDIA GPUs with NVENC/NVDEC. Any GPU [listed here](https://developer.nvidia.com/video-encode-and-decode-gpu-support-matrix-new) with those chips should theoretically work. Note that different models may be subject to different session limits that restrict the amount of video that can be transcoded.
* See [this document which lists tested driver versions](https://github.com/livepeer/go-livepeer/blob/master/doc/gpu.md) on the NVIDIA cards. 

| GPU Model                     | Tested Transcoding | Tested Concurrent Ethash Mining | Notes                                                                                                  |
| ----------------------------- | ------------------ | ------------------------------- | ------------------------------------------------------------------------------------------------------ |
| NVIDIA GeForce GTX 1060       | :white_check_mark: | :white_check_mark:              |                                                                                                        |
| NVIDIA GeForce GTX 1070       | :white_check_mark: |                                 |                                                                                                        |
| NVIDIA GeForce GTX 1070 Ti    | :white_check_mark: | :white_check_mark:              |                                                                                                        |
| NVIDIA GeForce GTX 1080       | :white_check_mark: |                                 |                                                                                                        |
| NVIDIA GeForce GTX 1080 Ti    | :white_check_mark: |                                 |                                                                                                        |
| NVIDIA Tesla T4               | :white_check_mark: |                                 |                                                                                                        |
| NVIDIA GeForce GTX 1660 Ti    | :white_check_mark: |                                 |                                                                                                        |
| NVIDIA GeForce GTX 1660 SUPER | :white_check_mark: |                                 |                                                                                                        |
| NVIDIA GeForce GTX 2080 Ti       | :white_check_mark: | :white_check_mark:              |
| NVIDIA GeForce RTX 3080       | :white_check_mark: | :white_check_mark:              | [Benchmarks](https://forum.livepeer.org/t/dual-ethash-mining-transcoding-w-rtx-3080-10g-cuda-mps/1161) |                                                                                                        |
| NVIDIA GeForce GTX 3090       | :white_check_mark: | :white_check_mark:              |     
| NVIDIA Titan V                | :white_check_mark: | :white_check_mark:              | [Patch](https://github.com/keylase/nvidia-patch)                                                       |
