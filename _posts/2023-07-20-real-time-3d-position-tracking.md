# Real-time 3D Position Tracking for Off-axis Rendering

I built a system that tracks where you are in 3D space using two webcams and adjusts a display to look 3D from your viewpoint. Here's how it works.

## How We Find Your Position in 3D

The system uses two webcams to figure out where you are. Think of each webcam as shooting out an invisible line toward you - where these lines meet is your position. Here's the specific process:

1. First, we find where you appear in each camera's view:

```python
def img_2d_to_3d(img_coord, fx, fy, wfov, hfov): 
    # Convert 2D image coordinates to 3D direction vector
    u = img_coord[0]  # x position in image
    v = img_coord[1]  # y position in image

    # Convert to normalized coordinates using camera parameters
    x = u / fx - math.tan(wfov / 2)
    y = v / fy - math.tan(hfov / 2) 
    z = 1 

    return np.array([x, y, z])
```

2. Then we find the rays from each camera to you:

```python
# Camera positions in 3D space
camera1_global_coord = np.array([-1, 0, 0])  # Left camera
camera2_global_coord = np.array([1, 0, 0])   # Right camera

# Get 2D positions from each camera
position_cam1 = find_color_coordinates_hsv(cur_img1)  # Find you in left image
position_cam2 = find_color_coordinates_hsv(cur_img2)  # Find you in right image

# Convert to 3D directions
coord_3d_cam1 = img_2d_to_3d(position_cam1, fx_focal_length, fy_focal_length, 
                            wfov_rad, hfov_rad)
coord_3d_cam2 = img_2d_to_3d(position_cam2, fx_focal_length, fy_focal_length, 
                            wfov_rad, hfov_rad)
```

3. Finally, we find where these rays intersect (your position):

```python
# Find intersection of the two rays
intersection_point = calculate_intersection_of_ray(
    camera1_global_coord,    # Position of first camera
    coord_3d_cam1,          # Direction from first camera
    camera2_global_coord,    # Position of second camera
    coord_3d_cam2           # Direction from second camera
)
```

## Sending Position Data to Unity

Once we have your position, we send it to Unity through shared memory:

```python
with mmap.mmap(-1, shm_size, tagname='Local\\memfile', access=mmap.ACCESS_WRITE) as mm:
    data = struct.pack('fff', intersection_point[0], 
                            intersection_point[1], 
                            intersection_point[2]) 
    mm.seek(0) 
    mm.write(data) 
    mm.flush()
```

## Results

<video width="100%" controls>
   <source src="/assets/videos/unity-offcenter-demo.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video>
This shows how the view changes based on viewer position.

<video width="100%" controls>
   <source src="/assets/videos/pre-kalman.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video>
Initial tracking was jittery.

<video width="100%" controls>
   <source src="/assets/videos/post-kalman.mp4" type="video/mp4">
   Your browser does not support the video tag.
</video>
Kalman filtering made the tracking much smoother.

Want to see the full code or learn more? Check out the GitHub repository: [link]
