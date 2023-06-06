# # Gesture-Based EC2 Instance Management

This project allows you to launch and terminate EC2 instances on Amazon Web Services (AWS) using finger gestures. The implementation leverages computer vision, machine learning, and AI techniques, along with Python programming.

## Prerequisites

Before running the code, ensure that you have the following prerequisites installed:

- [Anaconda](https://www.anaconda.com/) - Python distribution that simplifies package management and environment setup.
- [AWS CLI](https://aws.amazon.com/cli/) - Command-line interface for interacting with AWS services.

## Installation

To set up the project, follow these steps:

1. Clone the GitHub repository: [repository_link](insert_repository_link)
2. Navigate to the downloaded repository on your local machine.

3. Install the required Python libraries by running the following command:

   ```
   pip install -r requirements.txt
   ```

   This command will install the following libraries:

   - scikit-learn
   - numpy
   - pandas
   - matplotlib
   - boto3
   - opencv-python
   - urllib3
   - cvzone
   - mediapipe

## Configuration

To utilize the code successfully, you need to configure an IAM user on your AWS account and set up the AWS CLI on your Windows machine. Instructions for setting up an IAM user and configuring the AWS CLI can be found in the [AWS CLI User Guide](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html).

## Usage

After completing the installation and configuration steps, you can run the main code script to control EC2 instances using finger gestures.

The main code script is provided in the file `code`.

```python
import cv2

cap = cv2.VideoCapture(0)
allOs = []

import boto3
ec2 = boto3.resource("ec2")

def myOslaunch():
    instances = ec2.create_instances(
        ImageId="ami-0607784b46cbe5816",
        MinCount=1,
        MaxCount=1,
        InstanceType="t2.micro",
        SecurityGroupIds=["sg-0c859b5d5a14eaaf2"]
    )
    myid = instances[0].id
    allOs.append(myid)
    print(myid)
    print("total os:", len(allOs))
    

def osTerminate():
    if len(allOs) != 0:
        osdelete = allOs.pop()
        ec2.instances.filter(InstanceIds=[osdelete]).terminate()
        print("total os:", len(allOs))

from cvzone.HandTrackingModule import HandDetector
detector = HandDetector(maxHands=1)

while True:
    status, photo = cap.read()
    hand = detector.findHands(photo, draw=False)
    cv2.imshow("my photo", photo)
    if cv2.waitKey(100) == 13:
        break
    if hand:
        lmlist = hand[0]
        totalFinger = detector.fingersUp(lmlist)
        if totalFinger == [0, 1, 1, 0, 0]:
            print("launching a new instance")
            myOslaunch()
        elif totalFinger == [0, 1, 0, 0, 0]:
            print("Terminating the instance")
            osTerminate()
        else:
            print("show correct fingers")

cv2.destroyAllWindows()
allOs
cap.release()
```

**Explanation of the code:**

1. The code starts by importing the required libraries and initializing the video capture using OpenCV (`cv2.VideoCapture`).

2. An empty list called `allOs` is initialized. This list will keep track of the launched EC2

 instances.

3. The `boto3` library is used to create an EC2 resource object (`ec2`) that allows interaction with AWS EC2 services.

4. Two functions are defined:
   - `myOslaunch()`: This function creates a new EC2 instance using the `ec2.create_instances()` method. The instance is launched with specified parameters such as the Amazon Machine Image (AMI), instance count, instance type, and security group.
   - `osTerminate()`: This function terminates the most recently launched EC2 instance by using the `ec2.instances.filter()` method with the instance ID and calling the `terminate()` function.

5. The code imports the `HandDetector` class from the `cvzone.HandTrackingModule` library, which provides hand tracking capabilities using mediapipe.

6. A while loop is used to continuously capture frames from the video feed. The `detector.findHands()` function is called to detect hands in each frame.

7. The current frame is displayed using `cv2.imshow()`.

8. The program waits for a key press to break the loop and exit.

9. If a hand is detected, the `fingersUp()` function from the `HandDetector` class is called to determine the finger gestures made by the hand.

10. If the finger gesture corresponds to [0, 1, 1, 0, 0], the code executes the `myOslaunch()` function to launch a new EC2 instance.

11. If the finger gesture corresponds to [0, 1, 0, 0, 0], the code executes the `osTerminate()` function to terminate the most recently launched EC2 instance.

12. If the finger gesture does not match any predefined gestures, a message is printed to "show correct fingers".

13. Finally, the OpenCV windows are closed, and the video capture is released.

Feel free to customize and enhance the code as per your requirements.
