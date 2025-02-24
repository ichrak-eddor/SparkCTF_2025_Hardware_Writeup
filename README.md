# sCPPy Hardware Challenge - Writeup

This challenge provides a **sCPPy.sal** file, which is a capture from a **Saleae logic analyzer**. 


![image](https://github.com/user-attachments/assets/1807aea8-9cb1-460a-9efe-d0f130d0f9c2)


## Step 1: Identifying the Protocol

When analyzing logic analyzer captures, the first step is to determine which communication protocol is in use.

Looking at the signals, we notice that **only Channel 4** has meaningful activity, while the other channels appear to be just noise.

This suggests that the communication is using **UART protocol**, and our next step is to determine the **correct baud rate**.


![1](https://github.com/user-attachments/assets/4e6d295e-6175-4ca0-adfb-26de775a45dc)



## Step 2: Finding the Baud Rate

Observing the signal on Channel 4, we can see different messages being sent at varying **timing, length, and frequency**.
![2](https://github.com/user-attachments/assets/9a152d27-4f62-4fd3-b8cf-443abe4d3f64)



To identify the baud rate, we measure the **bit duration** of the signal, as baud rate is inversely related to bit duration.

### First Bit Duration

By analyzing the waveform, we find that the first message has a **bit duration of 418.5 µs**.

Now, we check which standard baud rate corresponds to a bit duration close to **418.5 µs**.
![3](https://github.com/user-attachments/assets/7750e0fa-3e5e-4c3f-8fa5-a69e0f1bf9b8)
![4](https://github.com/user-attachments/assets/4facd9f6-6040-4eb0-ad0f-f941392a0c90)



After testing, we successfully decode the second message.


![5](https://github.com/user-attachments/assets/f80af4b6-e487-4f65-a2cf-5f53d068af14)

### Second Bit Duration

For the next message, the measured bit duration is **4.5 µs**, which corresponds to a **baud rate of 230400**.
![6](https://github.com/user-attachments/assets/326d2e8d-e553-46f9-b428-2b5f9bee8dcc)
![7](https://github.com/user-attachments/assets/bfd0dc69-6770-4351-9bb3-61e92930feac)



Decoding it gives us the first part of the message:
![8](https://github.com/user-attachments/assets/98f15abb-9bcc-44ba-a7a2-bf99c08f2ab9)



Third Bit Duration

Another message appears with a **bit duration of 52.25 µs**, which matches a **baud rate of 19200**.

Decoding it gives us the final message.
![9](https://github.com/user-attachments/assets/77e0fcb0-0151-40fa-8818-d766ae9acaa7)
![11](https://github.com/user-attachments/assets/b35443c1-10a3-4106-b82d-a72236df97cf)
![10](https://github.com/user-attachments/assets/3484122c-0db6-4e5f-83ae-9a579e01365d)



## Step 3: Extracting the Flag

Once all messages are collected, we see that they form a **link**:

🔗 https://wokwi.com/projects/423329261637485569

Opening this **Wokwi project**, we find a circuit with an **OLED display** running a code that displays images.
![12](https://github.com/user-attachments/assets/2c5a380e-2fc5-4a5a-a0f7-734c6dbb75be)



Given that the challenge is named **sCPPy** (with "CPP"), and the description hints at looking for an **image**, we suspect a **bitmap-to-C++ conversion** is involved.

Looking at the code, we notice a **hidden section**:

```arduino
arduino
CopyEdit
const uint8_t  bitmap_icon_speed[] PROGMEM = {
	 0x00, 0x00, 0x00, 0x00, 0x77, 0x6e, 0x45, 0x44, 0x75, 0x64, 0x15, 0x45, 0x77, 0x45, 0x00, 0x05,
	 0x00, 0x00, 0x58, 0x10, 0x49, 0xd0, 0x59, 0x50, 0x49, 0x54, 0x19, 0xde, 0x00, 0x04, 0x00, 0x00
};

```

Using an **image-to-C++ decoder** like https://javl.github.io/image2cpp/, we **decode the bitmap**.

Since we know it's a **16x16 pixel** image, we extract the final flag:
![13](https://github.com/user-attachments/assets/3be4287a-7679-4dce-8c03-ab0a7d21ca85)
![14](https://github.com/user-attachments/assets/7699ccaf-ab3a-4455-b6d6-998d6c8d8c8d)




# Analyser Hardware Challenge - Writeup

In this challenge, we are provided with a **logic analyzer circuit** along with a `.vcd` file that can be opened using **PulseView** or other waveform analysis tools.
![15](https://github.com/user-attachments/assets/b01af012-0727-4f15-8886-24f246db4552)



Upon inspecting the captured signals, we can identify **six different channels**, each carrying distinct signals. Let's analyze them step by step.

### **Step 1: Identifying the UART Signal**

- The **D0 channel** shows a single, isolated signal without any parallel activity.
- Given its characteristics, we infer that this is a **UART (Universal Asynchronous Receiver-Transmitter) signal**.
    
   
    ![16](https://github.com/user-attachments/assets/b1cd0be6-1570-4351-a5b6-61270e95ecda)


### **Step 2: Determining the Baud Rate**

- To decode the UART transmission, we need to determine the **baud rate**.
- By measuring the **bit duration**, we find it to be **26µs**, which corresponds to a **baud rate of 38,400**.
  ![23](https://github.com/user-attachments/assets/7e57c2dc-a6fe-4e46-a182-650e57d4d1d1)

![21](https://github.com/user-attachments/assets/be5ad8e8-78c2-4e04-bd75-d582b45dfe46)

## **Extracting and Decoding the UART Data**

- From the UART transmission, we extract the following **hex dump**:
    

![22](https://github.com/user-attachments/assets/03f2c184-07ce-479c-894d-dab8da246c26)
- We can use **CyberChef** or any other tool to decode it. However, in this case, we wrote a simple Python script to **XOR-decrypt** the extracted data:
    
    ```c
    def xor_decrypt(hex_string, key=0x55):
        encrypted_bytes = bytes.fromhex(hex_string)
        decrypted_bytes = bytes(b ^ key for b in encrypted_bytes)
    
        
        return decrypted_bytes.decode(errors="ignore")
    
    hex_input = "66 69 72 73 74 20 06 25 34 27 3E 16 01 13 2E 01 3D 3C 66 60 32 33 0D 0A 66 69 72 73 74 20 06 25 34 27 3E 16 01 13 2E 01 3D 3C 66 60 32 33 0D 0A 66 69 72 73 74 20 06 25 34 27 3E 16 01 13 2E 01 3D 3C 66 60 32 33 0D 0A 53 65 63 72 65 74 3A 20 34 0D 0A 66 69 72 73 74 20 06 25 34 27 3E"
    
    decrypted_message = xor_decrypt(hex_input.replace(" ", ""))
    print("Decrypted Message:", decrypted_message)
    
    ```
    

### **Step 3: Detecting PWM Signal**

- The **D6 channel** exhibits a **pulse-width modulation (PWM) signal**, which is commonly used for motor control, LED dimming, or other modulated outputs.

### **Step 4: Recognizing SPI and I2C Protocols**

![17](https://github.com/user-attachments/assets/58929ad2-ea21-482c-9f91-305a20647d96)

- We observe **two distinct clock signals**, one on **channel D3** and another on **channel D4**.
- Since **SPI (Serial Peripheral Interface) and I2C (Inter-Integrated Circuit)** both rely on clock signals, we need to determine which protocol corresponds to each clock.

### **Differentiating I2C and SPI:**

- **I2C** operates with **two lines**:
    - **SDA (Serial Data Line)**
    - **SCL (Serial Clock Line)**
    - These lines typically have **pull-up resistors**, and communication follows a **start condition, address frame, data frame, and stop condition**.
- **SPI** requires multiple lines:
    - **SCK (Serial Clock)**
    - **MOSI (Master Out Slave In)**
    - **MISO (Master In Slave Out)**
    - **CS (Chip Select)** (optional, depending on implementation)

### **Which is Which?**

- By examining the captured signals, we notice that one clock line is always associated with **two data lines** (suggesting **MOSI and MISO**), while the other has **only a single data line** (suggesting **SDA** for I2C).
- Based on this observation:
    - **Channel D3 corresponds to I2C** (one data line and one clock).
    - **Channel D4 corresponds to SPI** (two data lines and one clock).

### **SPI Specific Observations**

- In this case, we might have **only MISO** or **only MOSI**, meaning we could be looking at:
    - A **unidirectional SPI transaction** (only data being sent or received)..
    - We only observe **MOSI (Master Out Slave In)** and **no MISO (Master In Slave Out)**.
    - This suggests that:
        - The master is transmitting data to the slave.
        - The slave might not be responding, or its response (MISO) is not captured in the logic analyzer.
        - The flag might be embedded in the master’s transmission rather than relying on a response.

### **Step 5: Extracting Data from SPI**

- Checking the **I2C** bus led to no useful results.
- However, analyzing the **SPI communication on channels D2 and D4**, we retrieve the **second part of the flag**!


![25](https://github.com/user-attachments/assets/2689a343-78bd-48e9-8f17-34477f5cf7e1)
![24](https://github.com/user-attachments/assets/005a2e06-9bbc-4ad5-8377-c81ee8fdba5a)


# Layer-Circuit
![18](https://github.com/user-attachments/assets/7675d2a9-123e-4ee4-86c5-2f6e2bfd7e20)

![20](https://github.com/user-attachments/assets/5d9e58db-2fe9-4e9a-9751-b02d5df3e10e)
![19](https://github.com/user-attachments/assets/2d0c3f18-23ad-4b57-af33-954adf685930)


### **XOR Operations**

A1=X4⊕X7

A2=X3⊕X10

A3=X1⊕X5

A4=X2⊕X9

A5=X6⊕X15

B1=X8⊕X4‾

B2=X11⊕X6‾

B3=X13⊕X3‾

B4=X12⊕X5‾

B5=X14⊕X2‾

### **NOR Operations **

N1=NOR(A1,A2)

N2=NOR(A3,A4)

N3=NOR(A5,B1)

N4=NOR(B2,B3)

N5=NOR(B4,B5)

N6=NOR(N1,N2)

N7=NOR(N3,N4)

N8=NOR(N5,X1)

N9=NOR(N6,N7)

N10=NOR(N8,N9)

### **end Operations**

C=AND(N6,N7,N8)
### **Final equation **

Y=AND(AND(A1,A2,A3,A4,A5),NOR(B1,B2,B3,B4,B5),C)

Now, we assign values to satisfy all conditions:

| Bit | Value |
| --- | --- |
| X1 | 0 |
| X2 | 0 |
| X3 | 0 |
| X4 | 1 |
| X5 | 1 |
| X6 | 0 |
| X7 | 0 |
| X8 | 0 |
| X9 | 1 |
| X10 | 1 |
| X11 | 1 |
| X12 | 0 |
| X13 | 0 |
| X14 | 0 |
| X15 | 1 |

and this is how we get the final flag

SparkCTF{000110001110001}





