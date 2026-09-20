# Practical 2
```
import cv2
import numpy as np
import matplotlib.pyplot as plt

def main():
    # ---------------------------------------------------------
    # 1. Image Acquisition & Format Conversion
    # ---------------------------------------------------------
    # Read the image (Ensure 'input.jpg' exists in your directory)
    img_path = 'input.jpg'
    img = cv2.imread(img_path)
    
    if img is None:
        print(f"Error: Could not read image at {img_path}")
        return

    # Convert format: Save the original JPEG image as a PNG
    cv2.imwrite('output_converted.png', img)
    print("Successfully converted input.jpg to output_converted.png")

    # ---------------------------------------------------------
    # 2. Color Space Operations
    # ---------------------------------------------------------
    # Convert BGR (OpenCV default) to Grayscale
    gray_img = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    
    # Convert BGR to HSV (Hue, Saturation, Value)
    hsv_img = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)

    # ---------------------------------------------------------
    # 3. Image Preprocessing: Smoothing (Blurring)
    # ---------------------------------------------------------
    # Apply Gaussian Blur to reduce noise and detail
    # (5, 5) is the kernel size, 0 is the standard deviation in X direction
    smoothed_img = cv2.GaussianBlur(img, (5, 5), 0)

    # ---------------------------------------------------------
    # 4. Image Preprocessing: Sharpening
    # ---------------------------------------------------------
    # Define a custom sharpening kernel
    sharpening_kernel = np.array([[-1, -1, -1],
                                  [-1,  9, -1],
                                  [-1, -1, -1]])
    
    # Apply the kernel using filter2D
    sharpened_img = cv2.filter2D(img, -1, sharpening_kernel)

    # ---------------------------------------------------------
    # 5. Image Preprocessing: Histogram Equalization
    # ---------------------------------------------------------
    # Histogram equalization enhances contrast but requires a single-channel (grayscale) image
    equalized_img = cv2.equalizeHist(gray_img)

    # ---------------------------------------------------------
    # Display Results using OpenCV
    # ---------------------------------------------------------
    # Resize windows for better viewing if the image is large
    scale_percent = 50 
    width = int(img.shape[1] * scale_percent / 100)
    height = int(img.shape[0] * scale_percent / 100)
    dim = (width, height)

    cv2.imshow('Original BGR', cv2.resize(img, dim))
    cv2.imshow('Grayscale', cv2.resize(gray_img, dim))
    cv2.imshow('Smoothed (Gaussian Blur)', cv2.resize(smoothed_img, dim))
    cv2.imshow('Sharpened', cv2.resize(sharpened_img, dim))
    cv2.imshow('Histogram Equalized', cv2.resize(equalized_img, dim))

    print("Press any key on the image windows to close them.")
    cv2.waitKey(0)
    cv2.destroyAllWindows()

if __name__ == "__main__":
    main()
```

# Practical 3
```
import cv2
import numpy as np

def main():
    # Load image in grayscale (required for Sobel and Canny)
    # Also load in color to clearly draw FAST corners later
    img_gray = cv2.imread('input.jpg', cv2.IMREAD_GRAYSCALE)
    img_color = cv2.imread('input.jpg')

    if img_gray is None:
        print("Error: Could not read 'input.jpg'. Please check the file path.")
        return

    # ---------------------------------------------------------
    # 1. Sobel Edge Detection
    # ---------------------------------------------------------
    # Compute gradients along the X and Y axes
    # cv2.CV_64F is used to capture negative slopes/gradients
    sobel_x = cv2.Sobel(img_gray, cv2.CV_64F, 1, 0, ksize=3)
    sobel_y = cv2.Sobel(img_gray, cv2.CV_64F, 0, 1, ksize=3)

    # Convert back to 8-bit unsigned integers
    abs_sobel_x = cv2.convertScaleAbs(sobel_x)
    abs_sobel_y = cv2.convertScaleAbs(sobel_y)

    # Combine the two gradients into a single edge map
    sobel_combined = cv2.addWeighted(abs_sobel_x, 0.5, abs_sobel_y, 0.5, 0)

    # ---------------------------------------------------------
    # 2. Canny Edge Detection
    # ---------------------------------------------------------
    # 100 is the lower threshold, 200 is the upper threshold for hysteresis
    canny_edges = cv2.Canny(img_gray, threshold1=100, threshold2=200)

    # ---------------------------------------------------------
    # 3. FAST Corner Detection
    # ---------------------------------------------------------
    # Initialize the FAST detector
    fast = cv2.FastFeatureDetector_create()

    # Detect keypoints in the grayscale image
    keypoints = fast.detect(img_gray, None)

    # Draw the detected keypoints (corners) onto the original color image
    # cv2.DRAW_MATCHES_FLAGS_DRAW_RICH_KEYPOINTS draws the keypoint size and orientation
    fast_image = cv2.drawKeypoints(img_color, keypoints, None, color=(0, 255, 0))

    # ---------------------------------------------------------
    # Display Results
    # ---------------------------------------------------------
    # Resize function for large images
    def resize_img(image, scale=50):
        width = int(image.shape[1] * scale / 100)
        height = int(image.shape[0] * scale / 100)
        return cv2.resize(image, (width, height))

    cv2.imshow('Original Image', resize_img(img_gray))
    cv2.imshow('Sobel X', resize_img(abs_sobel_x))
    cv2.imshow('Sobel Y', resize_img(abs_sobel_y))
    cv2.imshow('Sobel Combined', resize_img(sobel_combined))
    cv2.imshow('Canny Edges', resize_img(canny_edges))
    cv2.imshow('FAST Corners', resize_img(fast_image))

    print(f"Total FAST corners detected: {len(keypoints)}")
    print("Press any key on the image windows to close them.")
    
    cv2.waitKey(0)
    cv2.destroyAllWindows()

if __name__ == "__main__":
    main()
```

# Practical 4
```
import cv2
import numpy as np
import matplotlib.pyplot as plt

def main():
    # 1. Load images in grayscale
    # Replace these paths with your actual image files
    img1_path = 'image1.jpg' 
    img2_path = 'image2.jpg'
    
    img1 = cv2.imread(img1_path, cv2.IMREAD_GRAYSCALE)
    img2 = cv2.imread(img2_path, cv2.IMREAD_GRAYSCALE)
    
    if img1 is None or img2 is None:
        print("Error: Could not load images. Please check the file paths.")
        return

    # =========================================================================
    # PART A: ORB Feature Detection and Matching
    # =========================================================================
    print("Processing ORB...")
    
    # Initialize ORB detector
    orb = cv2.ORB_create()
    
    # Detect keypoints and compute descriptors
    kp1_orb, des1_orb = orb.detectAndCompute(img1, None)
    kp2_orb, des2_orb = orb.detectAndCompute(img2, None)
    
    # Create BFMatcher object
    # ORB produces binary descriptors, so we use NORM_HAMMING
    # crossCheck=True ensures mutual matching for better accuracy
    bf_orb = cv2.BFMatcher(cv2.NORM_HAMMING, crossCheck=True)
    
    # Match descriptors
    matches_orb = bf_orb.match(des1_orb, des2_orb)
    
    # Sort them in the order of their distance (lower distance is better)
    matches_orb = sorted(matches_orb, key=lambda x: x.distance)
    
    # Draw first 50 matches
    img_orb_matches = cv2.drawMatches(
        img1, kp1_orb, img2, kp2_orb, matches_orb[:50], None, 
        flags=cv2.DrawMatchesFlags_NOT_DRAW_SINGLE_POINTS
    )

    # =========================================================================
    # PART B: SIFT Feature Detection and Matching (with Lowe's Ratio Test)
    # =========================================================================
    print("Processing SIFT...")
    
    # Initialize SIFT detector
    sift = cv2.SIFT_create()
    
    # Detect keypoints and compute descriptors
    kp1_sift, des1_sift = sift.detectAndCompute(img1, None)
    kp2_sift, des2_sift = sift.detectAndCompute(img2, None)
    
    # Create BFMatcher object
    # SIFT produces floating-point descriptors, so we use NORM_L2 (Euclidean distance)
    bf_sift = cv2.BFMatcher(cv2.NORM_L2, crossCheck=False)
    
    # knnMatch returns the top k best matches for each descriptor (k=2 for Lowe's test)
    raw_matches_sift = bf_sift.knnMatch(des1_sift, des2_sift, k=2)
    
    # Apply Lowe's ratio test
    # This filters out false positives by ensuring the best match is significantly 
    # better than the second-best match.
    good_sift_matches = []
    for m, n in raw_matches_sift:
        if m.distance < 0.75 * n.distance:
            good_sift_matches.append(m)
            
    # Draw good matches
    img_sift_matches = cv2.drawMatches(
        img1, kp1_sift, img2, kp2_sift, good_sift_matches, None, 
        flags=cv2.DrawMatchesFlags_NOT_DRAW_SINGLE_POINTS
    )

    # =========================================================================
    # PART C: Visualization using Matplotlib
    # =========================================================================
    # Convert BGR (OpenCV default) to RGB for Matplotlib if we were using color,
    # but since we used grayscale, we map it to a gray colormap or just show as is.
    
    plt.figure(figsize=(15, 10))
    
    plt.subplot(2, 1, 1)
    plt.imshow(img_orb_matches, cmap='gray')
    plt.title(f'ORB Matches (Top 50) - Hamming Distance')
    plt.axis('off')
    
    plt.subplot(2, 1, 2)
    plt.imshow(img_sift_matches, cmap='gray')
    plt.title(f'SIFT Matches ({len(good_sift_matches)} found) - L2 Distance & Lowe\'s Ratio')
    plt.axis('off')
    
    plt.tight_layout()
    plt.show()

if __name__ == "__main__":
    main()
```

# Practical 5
```
import cv2
import numpy as np

def main():
    # Read the original image in color and grayscale
    img_color = cv2.imread('input.jpg')
    if img_color is None:
        print("Error: Could not read 'input.jpg'.")
        return
    
    img_gray = cv2.cvtColor(img_color, cv2.COLOR_BGR2GRAY)
    
    # Resize for consistent viewing if necessary
    img_color = cv2.resize(img_color, (400, 400))
    img_gray = cv2.resize(img_gray, (400, 400))

    # ---------------------------------------------------------
    # 1. Thresholding (Otsu's Binarization)
    # ---------------------------------------------------------
    # Otsu's method automatically calculates the optimal threshold value
    ret, thresh_img = cv2.threshold(img_gray, 0, 255, cv2.THRESH_BINARY_INV + cv2.THRESH_OTSU)

    # ---------------------------------------------------------
    # 2. Region Growing (using OpenCV's FloodFill)
    # ---------------------------------------------------------
    region_grown_img = img_color.copy()
    h, w = region_grown_img.shape[:2]
    
    # FloodFill requires a mask that is 2 pixels larger than the image
    mask = np.zeros((h+2, w+2), np.uint8)
    
    # Define a seed point (e.g., the center of the image)
    seed_point = (w // 2, h // 2)
    
    # Define the replacement color (e.g., pure green)
    fill_color = (0, 255, 0) 
    
    # Define the tolerance for color differences
    lo_diff = (20, 20, 20)
    up_diff = (20, 20, 20)
    
    # Apply FloodFill (OpenCV's implementation of region growing)
    cv2.floodFill(region_grown_img, mask, seed_point, fill_color, lo_diff, up_diff, flags=4)

    # ---------------------------------------------------------
    # 3. Watershed Segmentation
    # ---------------------------------------------------------
    watershed_img = img_color.copy()
    
    # a. Noise removal using morphological opening
    kernel = np.ones((3,3), np.uint8)
    opening = cv2.morphologyEx(thresh_img, cv2.MORPH_OPEN, kernel, iterations=2)
    
    # b. Find sure background area (dilate the foreground)
    sure_bg = cv2.dilate(opening, kernel, iterations=3)
    
    # c. Find sure foreground area using Distance Transform
    dist_transform = cv2.distanceTransform(opening, cv2.DIST_L2, 5)
    ret, sure_fg = cv2.threshold(dist_transform, 0.7 * dist_transform.max(), 255, 0)
    
    # d. Find unknown region (border regions)
    sure_fg = np.uint8(sure_fg)
    unknown = cv2.subtract(sure_bg, sure_fg)
    
    # e. Marker labelling
    ret, markers = cv2.connectedComponents(sure_fg)
    
    # Add one to all labels so that sure background is not 0, but 1
    markers = markers + 1
    
    # Mark the unknown region with zero
    markers[unknown == 255] = 0
    
    # f. Apply Watershed
    cv2.watershed(watershed_img, markers)
    
    # Outline the segmented regions in red
    watershed_img[markers == -1] = [0, 0, 255]

    # ---------------------------------------------------------
    # Display Results
    # ---------------------------------------------------------
    cv2.imshow('Original Image', img_color)
    cv2.imshow('Thresholding (Otsu)', thresh_img)
    cv2.imshow('Region Growing (FloodFill)', region_grown_img)
    cv2.imshow('Watershed Boundaries', watershed_img)

    print("Press any key on the image windows to close them.")
    cv2.waitKey(0)
    cv2.destroyAllWindows()

if __name__ == "__main__":
    main()
```
