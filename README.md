# Setup
```
sudo apt-get install build-essential git python3-dev python3-pip libopenexr-dev libxi-dev \
                     libglfw3-dev libglew-dev libomp-dev libxinerama-dev libxcursor-dev

sudo apt-get install colmap

git clone --recursive https://github.com/nvlabs/instant-ngp
cd instant-ngp

cmake . -B build
cmake --build build --config RelWithDebInfo -j

conda create -n ngp
pip install requirements.txt
```

# Steps

**Preprocess**

Create a folder and put the video inside. This way files for each scene will be separate.

```
python3 /home/pytholic/Desktop/Projects/uav_mapping/instant-ngp/scripts/colmap2nerf.py --video_in elevator.MOV --video_fps 10 --run_colmap --aabb_scale 16

python3 /home/pytholic/Desktop/Projects/uav_mapping/instant-ngp/scripts/colmap2nerf.py --images <img_dir> --run_colmap --aabb_scale 16
```

**Run nerf**

```
./build/testbed --scene data/custom_data/ios/elevator
```

**Loading snapshot**

```
./build/testbed --scene data/custom_data/ios/office --snapshot data/custom_data/ios/office/base.msgpack
```

**Generate video**

[https://www.youtube.com/watch?v=8GbENSmdVeE](https://www.youtube.com/watch?v=8GbENSmdVeE)

```
python scripts/run.py --mode nerf --scene data/nerf/fox --load_snapshot data/custom_data/ios/office/base.msgpack --video_camera_path data/custom_data/ios/office/base_cam.json --video_n_seconds 5 --video_fps 10 --width 1920 --height 1080
```

# Issues

Issue

```
qt.qpa.plugin: Could not load the Qt platform plugin "xcb"
```

Fix

```
export QT_QPA_PLATFORM=offscreen
```

Also add it to `bashrc`.

---

Another issue

```
WARNING: Your current options use the maximum number of threads on the machine 
to extract features. Exracting SIFT features on the CPU can consume a lot of 
RAM per thread for large images. Consider reducing the maximum image size 
and/or the first octave or manually limit the number of extraction threads. 
Ignore this warning, if your machine has sufficient memory for the current
settings.
```

Maybe because video quality is high.

This link might solve it → [https://colmap.github.io/faq.html](https://colmap.github.io/faq.html)

Worked by setting `--SiftExtraction.max_image_size 1024`.

```python
do_system(f"colmap feature_extractor --ImageReader.camera_model {args.colmap_camera_model} 
--ImageReader.camera_params \"{args.colmap_camera_params}\" --SiftExtraction.estimate_affine_shape=true \
--SiftExtraction.domain_size_pooling=true --ImageReader.single_camera 1 --database_path {db} --image_path {images} --SiftExtraction.max_image_size 1024")
```

---
