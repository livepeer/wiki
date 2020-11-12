# GPU Support in Livepeer

Livepeer enables node operators to transcode video on GPUs while concurrently mining cryptocurrencies. As there is a very wide range of GPU hardware out there, this document aims to crowdsource a list of specific GPU models that have been tested and verified to work on Livepeer. If you've tested an additional model, please submit an update to this document.

**Overview**

* Livepeer supports transcoding on NVidia GPUs with NVENC/NVDEC. Any GPU [listed here](https://developer.nvidia.com/video-encode-and-decode-gpu-support-matrix-new) with those chips should theoretically work. Note that different models have different driver versions and may be subject to session limits.
* See [this document which lists tested driver versions](https://github.com/livepeer/go-livepeer/blob/master/doc/gpu.md) on the NVidia cards. 

| GPU Model | Tested Transcoding | Tested Concurrent Mining | Notes | 
|------------|--------------------|--------------------------|---------|
| NVidia GeForce GTX 1060 | :white_check_mark: | :white_check_mark: | |
| NVidia GeForce GTX 1070 | :white_check_mark: | | | 
| NVidia GeForce GTX 1070 Ti | :white_check_mark: | :white_check_mark: | | 
| NVidia GeForce GTX 1080 | :white_check_mark: | | | 
| NVidia GeForce GTX 1080 Ti | :white_check_mark: | | | 
| NVidia Tesla T4 | :white_check_mark: | | | 
| NVidia GeForce GTX 1660 Ti | :white_check_mark: | | | 
| NVidia GeForce GTX 1660 SUPER | :white_check_mark: | | | 
