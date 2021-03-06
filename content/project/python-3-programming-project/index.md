---
date: "2022-03-15T00:00:00Z"
Summary: The Final Project for the Python 3 Programming course from the University of Michigan
Title: Python 3 Programming Project
---
# The Project #
1. This is a project with minimal scaffolding. Expect to use the the discussion forums to gain insights! It’s not cheating to ask others for opinions or perspectives!
2. Be inquisitive, try out new things.
3. Use the previous modules for insights into how to complete the functions! You'll have to combine Pillow, OpenCV, and Pytesseract
4. There are hints provided in Coursera, feel free to explore the hints if needed. Each hint provide progressively more details on how to solve the issue. This project is intended to be comprehensive and difficult if you do it without the hints.

### The Assignment ###
Take a [ZIP file](https://en.wikipedia.org/wiki/Zip_(file_format)) of images and process them, using a [library built into python](https://docs.python.org/3/library/zipfile.html) that you need to learn how to use. A ZIP file takes several different files and compresses them, thus saving space, into one single file. The files in the ZIP file we provide are newspaper images (like you saw in week 3). Your task is to write python code which allows one to search through the images looking for the occurrences of keywords and faces. E.g. if you search for "pizza" it will return a contact sheet of all of the faces which were located on the newspaper page which mentions "pizza". This will test your ability to learn a new ([library](https://docs.python.org/3/library/zipfile.html)), your ability to use OpenCV to detect faces, your ability to use tesseract to do optical character recognition, and your ability to use PIL to composite images together into contact sheets.

Each page of the newspapers is saved as a single PNG image in a file called [images.zip](./readonly/images.zip). These newspapers are in english, and contain a variety of stories, advertisements and images. Note: This file is fairly large (~200 MB) and may take some time to work with, I would encourage you to use [small_img.zip](./readonly/small_img.zip) for testing.

Here's an example of the output expected. Using the [small_img.zip](./readonly/small_img.zip) file, if I search for the string "Christopher" I should see the following image:
![Christopher Search](./readonly/small_project.png)
If I were to use the [images.zip](./readonly/images.zip) file and search for "Mark" I should see the following image (note that there are times when there are no faces on a page, but a word is found!):
![Mark Search](./readonly/large_project.png)

Note: That big file can take some time to process - for me it took nearly ten minutes! Use the small one for testing.


```python
import zipfile
from PIL import ImageDraw
from PIL import Image
from PIL import ImageOps
import pytesseract
import cv2 as cv
import numpy as np
import math
import os

# loading the face detection classifier
face_cascade = cv.CascadeClassifier('readonly/haarcascade_frontalface_default.xml')

# the rest is up to you!
xyz = {}
with zipfile.ZipFile('readonly/images.zip', 'r') as f:
    for starter in f.infolist():
        with f.open(starter) as file:
            img = Image.open(file).convert('RGB')
            xyz[starter.filename] = {'pil_img':img}
```


```python
for i in xyz.keys():
    text = pytesseract.image_to_string(xyz[i]['pil_img'])
    xyz[i]['text'] = text    
    cvc = np.array(xyz[i]['pil_img']) 
    vg = cv.cvtColor(cvc, cv.COLOR_BGR2GRAY)
    bxing = face_cascade.detectMultiScale(vg, 1.3, 5)
    xyz[i]['faces'] = []
    for x,y,w,h in bxing:
        face = xyz[i]['pil_img'].crop((x,y,x+w,y+h))
        xyz[i]['faces'].append(face)
    for face in xyz[i]['faces']:
        face.thumbnail((100,100),Image.ANTIALIAS)
```


```python
def s(name_of_individual):
    for i in xyz:
        if (name_of_individual in xyz[i]['text']) and (len(xyz[i]['faces']) != 0):
            print("Result found in file {}".format(i))
            h = math.ceil(len(xyz[i]['faces'])/5)
            cs=Image.new('RGB',(500, 100*h))
            accum = 0
            accum_2 = 0
            for img in xyz[i]['faces']:
                cs.paste(img, (accum, accum_2))
                if accum + 100 == cs.width:
                    accum = 0
                    accum_2 =accum_2 + 100
                else:
                    accum =accum + 100
                        
            display(cs)
        else:
            print("Result found in file {} \nBut there were no faces in that file\n\n".format(i))
    return
```


```python
s('Christopher')
```

    Result found in file a-0.png



    
![png](./Untitled_4_1.png)
    


    Result found in file a-1.png 
    But there were no faces in that file
    
    
    Result found in file a-10.png 
    But there were no faces in that file
    
    
    Result found in file a-11.png 
    But there were no faces in that file
    
    
    Result found in file a-12.png 
    But there were no faces in that file
    
    
    Result found in file a-13.png 
    But there were no faces in that file
    
    
    Result found in file a-2.png 
    But there were no faces in that file
    
    
    Result found in file a-3.png



    
![png](./Untitled_4_3.png)
    


    Result found in file a-4.png 
    But there were no faces in that file
    
    
    Result found in file a-5.png 
    But there were no faces in that file
    
    
    Result found in file a-6.png 
    But there were no faces in that file
    
    
    Result found in file a-7.png 
    But there were no faces in that file
    
    
    Result found in file a-8.png 
    But there were no faces in that file
    
    
    Result found in file a-9.png 
    But there were no faces in that file
    
    



```python
s('Mark')
```

    Result found in file a-0.png



    
![png](./Untitled_5_1.png)
    


    Result found in file a-1.png



    
![png](./Untitled_5_3.png)
    


    Result found in file a-10.png 
    But there were no faces in that file
    
    
    Result found in file a-11.png 
    But there were no faces in that file
    
    
    Result found in file a-12.png 
    But there were no faces in that file
    
    
    Result found in file a-13.png



    
![png](./Untitled_5_5.png)
    


    Result found in file a-2.png



    
![png](./Untitled_5_7.png)
    


    Result found in file a-3.png



    
![png](./Untitled_5_9.png)
    


    Result found in file a-4.png 
    But there were no faces in that file
    
    
    Result found in file a-5.png 
    But there were no faces in that file
    
    
    Result found in file a-6.png 
    But there were no faces in that file
    
    
    Result found in file a-7.png 
    But there were no faces in that file
    
    
    Result found in file a-8.png 
    But there were no faces in that file
    
    
    Result found in file a-9.png 
    But there were no faces in that file
    
    



```python
import zipfile

from PIL import Image
import pytesseract
import cv2 as cv
import numpy as np

# loading the face detection classifier
face_cascade = cv.CascadeClassifier('readonly/haarcascade_frontalface_default.xml')

'''
Your task is to write python code
which allows one to search through the images
looking for the occurrences of keywords and faces

E.g. if you search for "pizza",
it will return a contact sheet of all of the faces
which were located on the newspaper page which mentions "pizza".
'''

images = {}
name_list = [] 
def unzip_images(zip_name):
    zf = zipfile.ZipFile(zip_name)
    for each in zf.infolist():
        images[each.filename] = [Image.open(zf.open(each.filename))]
        name_list.append(each.filename)       
if __name__ == '__main__':
    unzip_images('readonly/images.zip')
    
    for name in name_list:
        img = images[name][0]   
        images[name].append(pytesseract.image_to_string(img).replace('-\n',''))       
        if 'Mark' in images[name][1]: 
            print('Results found in file',name)    
            try:
                faces = (face_cascade.detectMultiScale(np.array(img),1.35,4)).tolist()
                images[name].append(faces)
                faces_in_each = []
                for x,y,w,h in images[name][2]:
                    faces_in_each.append(img.crop((x,y,x+w,y+h)))            
                contact_sheet = Image.new(img.mode, (550,110*int(np.ceil(len(faces_in_each)/5))))
                x = 0
                y = 0
                for face in faces_in_each:
                    face.thumbnail((110,110))
                    contact_sheet.paste(face, (x, y))
                    if x+110 == contact_sheet.width:
                        x=0
                        y=y+110
                    else:
                        x=x+110
                        
                display(contact_sheet)
            except:
                print('no face found')
```

    Results found in file a-0.png



    
![png](./Untitled_6_1.png)
    


    Results found in file a-1.png



    
![png](./Untitled_6_3.png)
    


    Results found in file a-10.png
    no face found
    Results found in file a-13.png



    
![png](./Untitled_6_5.png)
    


    Results found in file a-2.png



    
![png](./Untitled_6_7.png)
    



```python
###End of Code!
```
