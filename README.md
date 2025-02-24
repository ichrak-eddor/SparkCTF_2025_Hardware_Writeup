This challenge provides a **sCPPy.sal** file, which is a capture from a **Saleae logic analyzer**. You can check it out here: <>

![image.png](attachment:42c81f6a-5005-4fcf-8d2b-0dae6cff4364:image.png)

## Step 1: Identifying the Protocol

When analyzing logic analyzer captures, the first step is to determine which communication protocol is in use.

Looking at the signals, we notice that **only Channel 4** has meaningful activity, while the other channels appear to be just noise.

This suggests that the communication is using **UART protocol**, and our next step is to determine the **correct baud rate**.

![image.png](attachment:66d63d98-6c1c-44cc-bbb3-f1a66965c92d:image.png)

## Step 2: Finding the Baud Rate

Observing the signal on Channel 4, we can see different messages being sent at varying **timing, length, and frequency**.

![image.png](attachment:1bb20777-6d01-4da2-969c-7e0e199868ca:image.png)

To identify the baud rate, we measure the **bit duration** of the signal, as baud rate is inversely related to bit duration.

### First Bit Duration

By analyzing the waveform, we find that the first message has a **bit duration of 418.5 µs**.

Now, we check which standard baud rate corresponds to a bit duration close to **418.5 µs**.

![image.png](attachment:160dbb10-c902-4646-8ab3-f79431fe23fd:image.png)

![image.png](attachment:93004d05-79d2-4758-9c76-d4834d5e9830:image.png)

After testing, we successfully decode the second message.

![image.png](attachment:6b94251f-3069-40b3-b221-9eec9a3046f6:image.png)

### Second Bit Duration

For the next message, the measured bit duration is **4.5 µs**, which corresponds to a **baud rate of 230400**.

![image.png](attachment:ea76c1f0-82e6-4017-aacc-34ebcefb8630:image.png)

![image.png](attachment:e96716bd-cc87-4a62-adb2-0fdc222d4efa:image.png)

Decoding it gives us the first part of the message:

![image.png](attachment:39ebf363-fdeb-45cf-8f52-d25c987633f8:image.png)

Third Bit Duration

Another message appears with a **bit duration of 52.25 µs**, which matches a **baud rate of 19200**.

Decoding it gives us the final message.

![image.png](attachment:b5d24452-3ab9-4581-83d7-fa2df5f8aad3:image.png)

![image.png](attachment:3b800673-2fda-44eb-8f57-bc011af829ac:image.png)

![image.png](attachment:a7962746-da73-4bc6-85ad-415c61f45493:image.png)

## Step 3: Extracting the Flag

Once all messages are collected, we see that they form a **link**:

🔗 https://wokwi.com/projects/423329261637485569

Opening this **Wokwi project**, we find a circuit with an **OLED display** running a code that displays images.

![image.png](attachment:63e33d71-8df0-40b4-b475-358ee6b61946:image.png)

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

![image.png](attachment:b585d49a-aade-405f-98f8-3a5c8609ca90:image.png)

![image.png](attachment:c02b114d-9ddf-45fa-8f67-87b5741a7e24:image.png)