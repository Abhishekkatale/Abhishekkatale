import cv2
import pickle
import cvzone
import numpy as np

#video feedsdfghjkl;
cap = cv2.VideoCapture("C:\\Users\\ABHISHEK\\Downloads\\CarParkProject12\\carPark.mp4")

with open('CarParkPos', 'rb') as f:
    posList = pickle.load(f)

width , height =107, 48

def checkParkingSpace(imgPro):

    spacecounter = 0

    for pos in posList:
        x,y = pos

        imgcrop = imgPro[y:y+height,x:x+width]
        #cv2.imshow(str(x*y),imgcrop)
        count = cv2.countNonZero(imgcrop)

        if count <900:
            color = (0,255,0)
            thickness = 5
            spacecounter +=1
        else:
            color = (0,0,255)
            thickness = 2
        cv2.rectangle(img, pos, (pos[0] + width, pos[1] + height),color,thickness)
        cvzone.putTextRect(img, str(count), (x, y + height - 3), scale=1, thickness=2, offset=0, colorR=color)

    cvzone.putTextRect(img, f'Free: {spacecounter}/{len(posList)}', (100,50g), scale=3, thickness=5, offset=20,colorR=(0,200,0))


while True:

    if cap.get(cv2.CAP_PROP_POS_FRAMES) == cap.get(cv2.CAP_PROP_FRAME_COUNT):
        cap.get(cv2.CAP_PROP_POS_FRAMES,0)
    success, img = cap.read()
    imgGray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    imgBlur = cv2.GaussianBlur(imgGray, (3, 3), 1)
    imgThreshould = cv2.adaptiveThreshold(imgBlur, 255, cv2.ADAPTIVE_THRESH_GAUSSIAN_C,
                                          cv2.THRESH_BINARY_INV, 25, 16)
    imgMedium = cv2.medianBlur(imgThreshould,5)
    kernal = np.ones((3, 3), np.uint8)
    imgDilate = cv2.dilate(imgMedium, kernal, iterations=1)


    checkParkingSpace(imgDilate)
    #for pos in posList:

    cv2.imshow("Image", img)
    #cv2.imshow("ImageBlur" ,imgBlur)
    #cv2.imshow("ImageThres", imgMedium)
    cv2.waitKey(10)
