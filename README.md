# color-detection
!pip install opencv-python pandas
import cv2
import pandas as pd
# Load the CSV file with color data
colors_df = pd.read_csv('colors.csv', names=['color', 'color_name', 'hex', 'R', 'G', 'B'], header=None)

# Initialize global variables
clicked = False
r = g = b = xpos = ypos = 0

# Function to find the closest color name based on RGB values
def get_color_name(R, G, B):
    min_diff = float('inf')
    color_name = ''
    for i in range(len(colors_df)):
        diff = abs(R - int(colors_df.loc[i, "R"])) + abs(G - int(colors_df.loc[i, "G"])) + abs(B - int(colors_df.loc[i, "B"]))
        if diff < min_diff:
            min_diff = diff
            color_name = colors_df.loc[i, "color_name"]
    return color_name

# Mouse click event function
def draw_function(event, x, y, flags, param):
    global b, g, r, xpos, ypos, clicked
    if event == cv2.EVENT_LBUTTONDBLCLK:  # Double-click to get the color
        clicked = True
        xpos, ypos = x, y
        b, g, r = img[y, x]
        b, g, r = int(b), int(g), int(r)

# Load the image
image_path = 'image.jpg'  # Replace with your image file name
img = cv2.imread(image_path)

# Resize the image for better visibility (Optional)
img = cv2.resize(img, (800, 600))

cv2.namedWindow('Image')
cv2.setMouseCallback('Image', draw_function)

while True:
    cv2.imshow('Image', img)
    if clicked:
        # Create a rectangle with the detected color and display the color name and RGB values
        cv2.rectangle(img, (20, 20), (750, 60), (b, g, r), -1)
        text = get_color_name(r, g, b) + f' R={r} G={g} B={b}'
        cv2.putText(img, text, (50, 50), cv2.FONT_HERSHEY_SIMPLEX, 0.8, (255, 255, 255), 2)

        # Adjust text color for better visibility
        if r + g + b >= 600:
            cv2.putText(img, text, (50, 50), cv2.FONT_HERSHEY_SIMPLEX, 0.8, (0, 0, 0), 2)

        clicked = False

    # Exit the program by pressing 'q'
    if cv2.waitKey(20) & 0xFF == ord('q'):
        break

cv2.destroyAllWindows()
