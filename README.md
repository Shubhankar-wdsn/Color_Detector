# Color_Detector
I made is project using OpenCV and Pandas in Python, The code returns the color information on the event of a double click on an image.
THE CODE : Color Detection in an Image

#Import Statements
Import cv2
Import pandas as pd
Import numpy as np
Import argparse

Argparse :  The argparse library makes it easy to write user-friendly command line interfaces. The program defines what arguments it requires, and the argparse will figure out how to parse those out of sys.argv. The argparse module also automatically generates help and usage messages and issue errors when the user gives invalid arguments. 

Documentation : https://docs.python.org/3/library/argparse.html


Ap = argparse.ArgumentParser()
ap.add_argument('-i', '--image', required = True, help = "ImagePath")
Args = vars(ap.parse_args())
img_path = args['image']

#Reading the image with opencv 
img = cv2.imread(img_path)

#Declaring global variables which will be used later on 
clicked = False
r = g = b = xpos = ypos = 0

Reading files with Pandas 
The pandas library is very useful when we need to perform various operations on data files like CSV. pd.read_csv() reads the CSV file and loads it into the pandas Data Frame. We have assigned each column with a name for easy accessing.

#Reading csv file with pandas and giving names to each column 
index = ["Color", "Color Name", "Hex Code", "R", "G", "B"]
csv = pd.read_csv('colors.csv', names = index, header = None)



Set a mouse callback event on window
First, we created a window in which the input image will display. Then, we set a callback function which will be called when a mouse event happens.

cv2.namedWindow('image')
cv2.setMouseCallback('image',draw_function)


Create Draw Function 
It will calculate the rgb values of the pixel which we double click. The function parameters have the event name, (x,y) coordinates of the mouse position, etc. In the function, we check if the event is double clicked then we calculate and set the r,g,b values along x,y positions of the mouse. 


def draw_function(event, x,y,flags,param):
	if event == cv2.EVENT_LBUTTONDBLCLK:
		global b,g,r,xpos,ypos, clicked
		clicked = True
		xpos = x
		ypos = y
		b,g,r = img[y,x]
		b = int(b)
		g = int(g)
		r = int(r)

Calculate Distance to get Color Name

We have the RGB values. Now, we need another function which will return us the color name from RGB values. To get color name, we calculate a distance d, which tells us how close we are to color and choose the one having the minimum distance. 

Our distance is calculated by the following formula : 

d = abs(Red - ithRedColor) + abs(Green - ithGreenColor) + abs(Blue - ithBlueColor)

def getColorName(R,G,B):
	minimum = 10000
	for i in range(len(csv)):
		d = abs(R- int(csv.loc[i,"R"])) + abs(G- int(csv.loc[i,"G"]))+ abs(B- int(csv.loc[i,"B"]))
		if(d<=minimum):
			minimum = d
			cname = csv.loc[i,"color_name"]
		return cname

Display Image on the Window

Whenever a double click event occurs, it will update the color name and RGB values on the window. 
Using the cv2.imshow() function, we draw the image on the window. When the user double clicks the window, we draw a rectangle and get the color name to draw text on the window using cv2.rectangle and cv2.putText() functions.

while(1):
	cv2.imshow("image",img)
	if (clicked):
	
		#cv2.rectangle(image, startpoint, endpoint, color, thickness) -1 thickness fills rectangle entirely
		cv2.rectangle(img,(20,20), (750,60), (b,g,r), -1)
		
		#Creating text string to display ( Color name and RGB values )
		text = getColorName(r,g,b) + ' R='+ str(r) + ' G='+ str(g) + ' B='+ str(b)
		
		#cv2.putText(img,text,start,font(0-7), fontScale, color, thickness, lineType, (optional bottomLeft bool) )
		cv2.putText(img, text,(50,50),2,0.8,(255,255,255),2,cv2.LINE_AA)
		
		#For very light colours we will display text in black colour
		if(r+g+b>=600):
			cv2.putText(img, text,(50,50),2,0.8,(0,0,0),2,cv2.LINE_AA)
			
		clicked=False
		
	#Break the loop when user hits 'esc' key 
	if cv2.waitKey(20) & 0xFF ==27:
	break
	
cv2.destroyAllWindows()

Run Python File
Now we can run the Python file, we need to give -I argument and the full path of the image if it is in another directory. 


>> python color_detection.py -i <add your image path here>
