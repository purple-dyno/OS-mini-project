1.    INTRODUCTION:
Sleeping barber problem initially has only one barber, and later on can be escalated to use more barbers at the same barber shop. However primarily, the problem is stated as having one barber at one barber shop. This barber has one chair for cutting hair and a waiting room for customers with N chairs.The barber finishes cutting a customer’s hair and goes to check if there are any customers in the waiting room; if there are, he brings one of them to the chair, cuts his hair and goes to check for more customers. If there are no more customers waiting, he returns to a sleep state. Now, when another customer arrives, the customer looks at the barber to see what he is doing. If the barber is sleeping, this customer gladly walks to him, wakes him up, sits in the chair and gets his haircut. If the barber was cutting hair, the new customer simply walks into the waiting room and waits, but if there are no empty chairs in the waiting room, he leaves. Despite its simplicity, it’s very complicated to synchronize this barber shop to make it work perfectly all the time. The solution is to implement a simple mutual exclusion lock that will ensure that all the participants in one particular moment can change state only one at a time.
 
2. SLEEPING BARBER PROBLEM:
Statement and Explanation of the Problem
Another classical IPC problem takes place in a barber shop. The barber shop has one barber, one barber chair, and n chairs for waiting customers, if any, to sit on. If there are no customers present, the barber sits down in the barber chair and falls asleep. When a customer arrives, he has to wake up the sleeping barber. If additional customers arrive while the barber is cutting a customer's hair, they either sit down (if there are empty chairs) or leave the shop (if all chairs are full). The problem is to program the barber and the customers without getting into race conditions. This problem is similar to various queueing situations, such as a multi person helpdesk with a computerized call waiting system for holding a limited number of incoming calls.
Our solution uses three semaphores, customers, which counts waiting customers (excluding the customer in the barber chair, who is not waiting), barbers, the number of barbers (0 or 1) who are idle, waiting for customers, and mutex, which is used for mutual exclusion. We also need a variable, waiting, which also counts the waiting customers. The reason for having waiting is that there is no way to read the current value of a semaphore. In this solution, a customer entering the shop has to count the number of waiting customers. If it is less than the number of chairs, he stays; otherwise, he leaves.
When the barber shows up for work in the morning, he executes the procedure barber, causing him to block on the semaphore customers because it is initially 0. The barber then goes to sleep. He stays asleep until the first customer shows up.
 	When a customer arrives, he executes the customer, starting by acquiring mutex to enter a critical region. If another customer enters shortly thereafter, the second one will not be able to do anything until the first one has released mutex. The customer then checks to see if the number of waiting customers is less than the number of chairs. If not, he releases mutex and leaves without a haircut.
 	If there is an available chair, the customer increments the integer variable, waiting. Then he does an Up on the semaphore customers, thus waking up the barber. At this point, the customer and the barber are both awake. When the customer releases mutex, the barber grabs it, does some housekeeping, and begins the haircut.
 	When the haircut is over, the customer exits the procedure and leaves the shop. Unlike our earlier examples, there is no loop for the customer because each one gets only one haircut. The barber loops, however, to try to get the next customer. If one is present, a haircut is given. If not, the barber goes to sleep.
 	As an aside, it is worth pointing out that although the readers and writers and sleeping barber problems do not involve data transfer, they still belong to the area of IPC because they involve synchronization between multiple processes.
 


3. PROBLEM DEFINITION:
To simulate Sleeping Barbers Problem for the purpose of  mutual exclusion using semaphores and allows Process Synchronization. 
4. CODE IMPLEMENTATION:
from threading import *
import time
from tkinter import *
from PIL import ImageTk, Image

custready = 0
access = 1
noofseats = 4
come = 0

def SBP():
    global c
    root = Tk()
    root.title("Sleeping Barber Problem Solution")
    root.geometry("1920x1080")
    c = Canvas(root, bg='white', height=1080, width=1920)
    c.pack()
    img = ImageTk.PhotoImage(Image.open("images/bg.jpg"))
    c.create_rectangle(100, 100, 1400, 650, fill='white')
    c.create_rectangle(110, 110, 500, 640, fill='white')
    c.create_rectangle(510, 110, 1100, 640, fill='white')
    c.create_rectangle(1110, 110, 1390, 640, fill='white')
    fnt = ('Times', 28, 'bold', 'underline')
    c.create_text(
        500, 50, text="SLEEPING BARBER PROBLEM SOLUTION . . .", font=fnt, fill='BLACK')
    fnt = ('Times', 28, 'bold', 'underline')
    c.create_text(300, 150, text="MAIN ROOM", font=fnt, fill='black')
    c.create_text(800, 150, text="WAITING ROOM", font=fnt, fill='black')
    c.create_text(1250, 150, text="ENTRY DOOR", font=fnt, fill='black')
    fnt = ('Times', 30, 'bold')
    b1 = Button(c, text="ENTER", font=fnt, command=lambda: buttonClick())
    b1.place(x=650, y=690, width=200, height=70)
    root.mainloop()

def buttonClick():
    global come
    come = 1

def signal1(s):
    global custready
    custready = s
    custready = custready+1

def wait1(s):
    global custready, c, img19
    cnt = 0
    custready = s
    if custready == 0:
        print("Barber is Sleeping")
        img1 = ImageTk.PhotoImage(Image.open("images/sleepingbarber.png"))
        c.create_image(170, 230, anchor=NW, image=img1)
        img2 = ImageTk.PhotoImage(Image.open("images/chairs.png"))
        c.create_image(620, 250, anchor=NW, image=img2)
    while(custready <= 0):
        cnt = 1
    if cnt == 1:
        cnt = 0
        print("Barber Wake Up")
        img3 = ImageTk.PhotoImage(Image.open("images/wakeupbarber.png"))
        c.create_image(170, 230, anchor=NW, image=img3)
        time.sleep(1)
        img19 = ImageTk.PhotoImage(Image.open("images/readybarber.png"))
        c.create_image(170, 230, anchor=NW, image=img19)
    custready = custready-1

def signal2(s):
    global access
    access = s
    access = access+1

def wait2(s):
    global access
    access = s
    while(access <= 0):
        pass
    access = access-1

class Barber(Thread):
    def run(self):
        global custready, access, noofseats, c
        while(True):
            wait1(custready)
            wait2(access)
            time.sleep(0.2)
            print("Customer Enters into Main Room")
            if custready == 1:
                img4 = ImageTk.PhotoImage(Image.open("images/1person.png"))
                c.create_image(620, 250, anchor=NW, image=img4)
            elif custready == 2:
                img5 = ImageTk.PhotoImage(Image.open("images/2person.png"))
                c.create_image(620, 250, anchor=NW, image=img5)
            elif custready == 3:
                img6 = ImageTk.PhotoImage(Image.open("images/3person.png"))
                c.create_image(620, 250, anchor=NW, image=img6)
            elif custready == 4:
                img7 = ImageTk.PhotoImage(Image.open("images/4person.png"))
                c.create_image(620, 250, anchor=NW, image=img7)
            else:
                img8 = ImageTk.PhotoImage(Image.open("images/chairs.png"))
                c.create_image(620, 250, anchor=NW, image=img8)
            time.sleep(1)
            noofseats = noofseats+1
            print("started cutting")
            img9 = ImageTk.PhotoImage(Image.open("images/workingbarber.png"))
            c.create_image(170, 230, anchor=NW, image=img9)
            time.sleep(10)
            print("Cutting complete")
            img10 = ImageTk.PhotoImage(Image.open("images/readybarber.png"))
            c.create_image(170, 230, anchor=NW, image=img10)
            time.sleep(1)
            signal2(access)

class Customer(Thread):
    def run(self):
        global custready, access, noofseats, come, c
        while(True):
            if come == 1:
                come = 0
                img16 = ImageTk.PhotoImage(Image.open("images/entering.png"))
                i16 = c.create_image(1145, 200, anchor=NW, image=img16)
                time.sleep(1)
                c.delete(i16)
                if noofseats > 0:
                    print("Customer Enters into Waiting Room")
                    noofseats = noofseats-1
                    signal1(custready)
                    if custready == 1:
                        img11 = ImageTk.PhotoImage(
                            Image.open("images/1person.png"))
                        c.create_image(620, 250, anchor=NW, image=img11)
                    elif custready == 2:
                        img12 = ImageTk.PhotoImage(
                            Image.open("images/2person.png"))
                        c.create_image(620, 250, anchor=NW, image=img12)
                    elif custready == 3:
                        img13 = ImageTk.PhotoImage(
                            Image.open("images/3person.png"))
                        c.create_image(620, 250, anchor=NW, image=img13)
                    elif custready == 4:
                        img14 = ImageTk.PhotoImage(
                            Image.open("images/4person.png"))
                        c.create_image(620, 250, anchor=NW, image=img14)
                    else:
                        img15 = ImageTk.PhotoImage(
                            Image.open("images/chairs.png"))
                        c.create_image(620, 250, anchor=NW, image=img15)
                    time.sleep(1)
                elif noofseats == 0:
                    img17 = ImageTk.PhotoImage(
                        Image.open("images/nospace.png"))
                    i17 = c.create_image(1145, 200, anchor=NW, image=img17)
                    time.sleep(0.5)
                    c.delete(i17)
                    img18 = ImageTk.PhotoImage(
                        Image.open("images/leaving.png"))
                    i18 = c.create_image(1145, 200, anchor=NW, image=img18)
                    time.sleep(1)
                    c.delete(i18)
                    print("Customer Enters But There is No Space, Customer Leaves")

b = Barber()
c = Customer()

b.start()
c.start()

SBP()
 
 
 
 
 
 
 
 
 
 
 
 
 
5. RESULTS:



 
 6. CONCLUSION:
In Operating System, we have learnt that Process Synchronization which means sharing system resources by processes in such a way that Concurrent access to shared data is handled thereby minimizing the chance of inconsistent data. Maintaining data consistency demands mechanisms to ensure synchronized execution of cooperating processes. As the synchronization hardware solution is not easy to implement for everyone, a strict software approach called Mutex Locks was introduced. In this approach, in the entry section of code, a LOCK is acquired over the critical resources modified and used inside the critical section, and in the exit section that LOCK is released.
As the resource is locked while a process executes its critical section hence no other process can access it.	
One reason this problem is not widely used in the real world is that to use it, the operating system must know the maximum amount of resources that every process is going to need at all times.
