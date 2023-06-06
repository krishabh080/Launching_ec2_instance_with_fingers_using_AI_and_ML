# # Gesture-Based EC2 Instance Management

This project allows you to launch and terminate EC2 instances on Amazon Web Services (AWS) using finger gestures. The implementation leverages computer vision, machine learning, and AI techniques, along with Python programming.

## Prerequisites

Before running the code, ensure that you have the following prerequisites installed:

- [Anaconda](https://www.anaconda.com/) - Python distribution that simplifies package management and environment setup.
- [AWS CLI](https://aws.amazon.com/cli/) - Command-line interface for interacting with AWS services.

## Installation

To set up the project, follow these steps:

1. Clone the GitHub repository: [repository_link](https://github.com/krishabh080/Launching_ec2_instance_with_fingers_using_AI_and_ML.git)
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

To utilize the code successfully, you need to create an IAM user on your AWS account.
1. Go to IAM Dashboard , click on users:
<img width="277" alt="image" src="https://github.com/krishabh080/Launching_ec2_instance_with_fingers_using_AI_and_ML/assets/86287145/a4ff4e7a-be76-4baf-a506-1d6185906797">


2. create on add users:
<img width="172" alt="image" src="https://github.com/krishabh080/Launching_ec2_instance_with_fingers_using_AI_and_ML/assets/86287145/25d5faff-45d0-4750-82aa-383ea47a6940">


3. Add username.


4. click on "Attach Policies Directly" , attach pemission:

<img width="393" alt="image" src="https://github.com/krishabh080/Launching_ec2_instance_with_fingers_using_AI_and_ML/assets/86287145/94c4dbef-2d23-416e-9077-f5684d65ded2">


5. Click on create user.


6. Go to the Security Credentials section of IAM user and create access and secret key, then configure them on your CLI on windows.
<img width="426" alt="image" src="https://github.com/krishabh080/Launching_ec2_instance_with_fingers_using_AI_and_ML/assets/86287145/d14ec6e8-78e1-4d3e-bf6b-97f0eaec0360">


<img width="646" alt="image" src="https://github.com/krishabh080/Launching_ec2_instance_with_fingers_using_AI_and_ML/assets/86287145/f27f1bdc-d4c4-48fd-b9d1-b5241034cf70">


<img width="452" alt="image" src="https://github.com/krishabh080/Launching_ec2_instance_with_fingers_using_AI_and_ML/assets/86287145/c0eb1202-a0f7-4ea2-9df4-c00c6b27d2f4">


Copy access and secret keys:

<img width="392" alt="image" src="https://github.com/krishabh080/Launching_ec2_instance_with_fingers_using_AI_and_ML/assets/86287145/c6589500-0e64-4052-9f10-95127bc1de0a">


Add the user to your AWS CLI on windows.

<img width="517" alt="image" src="https://github.com/krishabh080/Launching_ec2_instance_with_fingers_using_AI_and_ML/assets/86287145/dce8a809-6e4c-42d5-9cb5-577483334570">

### Creating Security Group and Adding Inbound Rules

To create a security group and add inbound rules, follow these steps:

1. Go to the AWS Management Console and navigate to the EC2 service.

2. Click on "Security Groups" in the sidebar menu.

3. Click on the "Create security group" button.

4. Provide a name for the security group and a description (optional).

5. In the "Inbound rules" section, click on "Add rule" to define the inbound traffic rules based on your requirements. Specify the protocol (e.g., TCP, UDP), port range, and source IP or CIDR range. Add as many inbound rules as needed.

6. Once you have defined all the inbound rules, click on the "Create security group" button to create the security group.

7. After the security group is created, locate its ID in the "Security group ID" column. Copy this ID for use in the `myOslaunch()` function in the code.

### Choosing an Amazon Machine Image (AMI)

To choose an AMI to launch an EC2 instance, follow these steps:

1. Go to the AWS Management Console and navigate to the EC2 service.

2. Click on "Instances" in the sidebar menu.

3. Click on the "Launch instance" button.

4. In the "Step 1: Choose an Amazon Machine Image (AMI)" section, select the desired AMI based on your requirements. You can choose from various pre-configured AMIs provided by AWS or select a community AMI.

5. Once you have selected the AMI, note down its AMI ID for use in the `myOslaunch()` function in the code.

## Usage

Ensure that you have performed the configuration steps mentioned above before running the code.

After configuring the security group and choosing the AMI, update the `myOslaunch()` function in the code with the appropriate values.

```python
def myOslaunch():
    instances = ec2.create_instances(
        ImageId="AMI_ID_HERE",  # Replace with the AMI ID you selected
        MinCount=1,
        MaxCount=1,
        InstanceType="Instance type", #Replace with the instance type you want to launch
        SecurityGroupIds=["SECURITY_GROUP_ID_HERE"]  # Replace with the security group ID you created
    )
    myid = instances[0].id
    allOs.append(myid)
    print(myid)
    print("total os:", len(allOs))
```

Replace `"AMI_ID_HERE"` with the AMI ID you selected from the AWS console. Replace `"SECURITY_GROUP_ID_HERE"` with the security group ID you created.

Now you can run the code to control EC2 instances using finger gestures.

**Note:** It is important to ensure that the security group allows the necessary inbound traffic for your application and that the chosen AMI is compatible with your requirements.


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
        ImageId="AMI_ID_HERE",
        MinCount=1,
        MaxCount=1,
        InstanceType="t2.micro",
        SecurityGroupIds=["SECURITY_GROUP_ID_HERE"]
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

10. If the finger gesture corresponds to [0, 1, 1, 0, 0], means "index and middle figers are up" then code executes the `myOslaunch()` function to launch a new EC2 instance.

11. If the finger gesture corresponds to [0, 1, 0, 0, 0], means "only index finger is up" then code executes the `osTerminate()` function to terminate the most recently launched EC2 instance.

12. If the finger gesture does not match any predefined gestures, a message is printed to "show correct fingers".

13. Finally, the OpenCV windows are closed, and the video capture is released.

Feel free to customize and enhance the code as per your requirements.
