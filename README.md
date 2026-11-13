# Thermal Image Super-Resolution (TISR)

Deep-learning super-resolution for thermal (long-wave infrared) images from the FLIR ADAS dataset,
aimed at improving image quality for detection in autonomous-driving and surveillance settings under
limited compute.

The repository contains two approaches:

| Approach | Folder | Models | Test images | Avg. PSNR | Avg. SSIM |
|---|---|---|---|---|---|
| Lightweight models, 128×128 | [`LOW DIMENSION LIGHTWEIGHT CNN AE`](Thermal%20Image%20Super%20Resolution/LOW%20DIMENSION%20LIGHTWEIGHT%20CNN%20AE/) | Autoencoder (37.6 k parameters) | 1,564 | 29.14 dB | 0.8191 |
| | | SRCNN (8.1 k parameters) | 1,564 | 28.87 dB | 0.8087 |
| SRCNN pipeline, 256×256, with YOLOv8 | [`SRCNN WITH PIPELINE FLIR THERMAL ONLY WITH YOLOV8`](Thermal%20Image%20Super%20Resolution/SRCNN%20WITH%20PIPELINE%20FLIR%20THERMAL%20ONLY%20WITH%20YOLOV8/) | SRCNN trained with a `tf.data` pipeline; pretrained YOLOv8 run on the super-resolved outputs | 1,564 | 33.18 dB | 0.8902 |

PSNR and SSIM are computed per image and averaged. The 256×256 values are the means of
`psnr_ssim_results.csv` in that folder. Results at the two image sizes are **not directly
comparable**.

## How low-resolution inputs are made

Thermal frames are downscaled with OpenCV and resized back to the working size, and the models learn
to restore the lost detail (input and output have the same size). Full details, model
architectures and training settings for the 128×128 models are in that folder's
[README](Thermal%20Image%20Super%20Resolution/LOW%20DIMENSION%20LIGHTWEIGHT%20CNN%20AE/README.md).

## Repository layout

```
Thermal Image Super Resolution/
├── LOW DIMENSION LIGHTWEIGHT CNN AE/                     128×128 autoencoder and SRCNN
│   ├── SRT LW LWIR AE CNN 128X128.ipynb                  data loading, training, evaluation
│   ├── SRTFLIRADASAE.h5, best_srcnn_model.h5             trained models
│   └── figures/                                          sample outputs, training loss
└── SRCNN WITH PIPELINE FLIR THERMAL ONLY WITH YOLOV8/    256×256 SRCNN + YOLOv8
    ├── FLIR_ADAS_..._YOLO_DETECTION.ipynb                pipeline notebook
    ├── best_srcnn_model.h5                               trained model
    └── psnr_ssim_results.csv                             per-image test metrics
```

## Running

The notebooks were run on Google Colab with TensorFlow/Keras. Install
`tensorflow opencv-python numpy matplotlib scikit-learn scikit-image` (and `ultralytics` for YOLOv8).
The FLIR ADAS dataset is **not** included; obtain it from Teledyne FLIR under its terms.

## Limitations

- Low-resolution inputs are simulated by downscaling, so results may differ on real low-resolution sensors.
- No PSNR/SSIM baseline for the degraded input itself is reported, so the gain over plain resizing is not measured.
- Single training runs, without repeated seeds.
- YOLOv8 is applied pretrained; detection accuracy on super-resolved versus original images is not yet quantified.

## Authors

**Mohammad Haris Mushtaq** and **Muhammad Obaida**, BS Artificial Intelligence, National University of
Technology (NUTECH), Islamabad.

## License

[Apache License 2.0](LICENSE)
