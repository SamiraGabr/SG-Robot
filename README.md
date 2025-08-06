`# Working on it >>>>`


# SG: Voice-Controlled AI Robot with Raspberry Pi & Google Gemini

-----

## Project Overview

This project showcases the development of an **intelligent, voice-controlled mobile robot** that combines the physical world of robotics with the advanced capabilities of Artificial Intelligence. At its core, the robot leverages a `Raspberry Pi` for hardware control and integrates with **`Google's Gemini AI model`** to facilitate natural language understanding and generation. The aim is to create an interactive companion capable of responding to spoken commands for movement and engaging in dynamic, context-aware conversations, thereby transforming a simple mobile platform into a truly intelligent agent.

I created this project to serve as an excellent foundation for those looking to delve into **AI on edge devices**, **robotics programming**, and **human-robot interaction** using accessible hardware and powerful cloud AI services.

-----
## Technologies Used

<p align="left">
  <!-- Raspberry Pi -->
  <img src="https://img.shields.io/badge/Raspberry%20Pi-C51A4A?style=for-the-badge&logo=raspberrypi&logoColor=white" alt="Raspberry Pi" />
  
  <!-- Python -->
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python" />
  
  <!-- Google Gemini -->
  <img src="https://img.shields.io/badge/Google%20Gemini-4285F4?style=for-the-badge&logo=google&logoColor=white" alt="Google Gemini" />
  
  <!-- speech_recognition -->
  <img src="https://img.shields.io/badge/SpeechRecognition-FFCC00?style=for-the-badge&logo=speechly&logoColor=black" alt="Speech Recognition" />
  
  <!-- gTTS -->
  <img src="https://img.shields.io/badge/gTTS-34A853?style=for-the-badge&logo=google&logoColor=white" alt="Google Text-to-Speech" />
  
  <!-- mpg321 -->
  <img src="https://img.shields.io/badge/mpg321-000000?style=for-the-badge&logo=linux&logoColor=white" alt="mpg321 player" />
  
  <!-- GPIO -->
  <img src="https://img.shields.io/badge/RPi.GPIO-F37626?style=for-the-badge&logo=raspberrypi&logoColor=white" alt="GPIO" />
  
  <!-- systemd -->
  <img src="https://img.shields.io/badge/systemd-000000?style=for-the-badge&logo=linux&logoColor=white" alt="systemd" />
</p>

-----

## Features

This robot project boasts a comprehensive set of features designed for intuitive interaction and intelligent behavior:

  * **1**
      * Utilizes the `speech_recognition` library with the Google Web Speech API for highly accurate and robust **Speech-to-Text (STT)** conversion.
      * Specifically configured for **Egyptian Arabic (`ar-EG`)** to ensure optimal understanding of local dialects, alongside general English commands.
      * Includes ambient noise adjustment to improve recognition in various environments.
  * **2**
      * Seamless integration with the **Google Gemini API (`gemini-2.5-flash` model)**, selected for its balance of computational efficiency and powerful language understanding.
      * Maintains **conversation context** throughout the interaction, allowing for fluid and natural dialogue where Gemini remembers previous turns. This enables the robot to answer follow-up questions and engage in sustained discussions.
      * Handles diverse conversational queries, from factual questions to creative prompts.
  * **3**
      * Direct control over two independent **DC motors** (TT motors) through a robust **L298N Motor Driver**.
      * Implements core movement primitives: **forward, backward, turn left, turn right, and immediate stop**.
      * GPIO pins are configured for **BCM numbering** for clarity and consistency.
      * Motor enable pins (`ENA`, `ENB`) are set to `HIGH` for continuous full-speed operation, simplifying the initial motor control logic.
  * **4**
      * Employs `gTTS` (Google Text-to-Speech) to synthesize responses, providing the robot with a natural-sounding voice.
      * Outputs speech in **Arabic (`ar`)**, making the robot more accessible and engaging for Arabic speakers.
      * Utilizes `mpg321` as a lightweight command-line audio player for efficient playback of generated speech.
  * **5**
      * Prioritizes **direct movement commands** for immediate execution, ensuring responsive physical control.
      * If a command isn't a direct movement instruction, it's intelligently routed to the **Gemini AI for interpretation and response generation**.
      * Includes a "shutdown" command to gracefully exit the script and clean up GPIO resources.
  * **6**
      * Incorporates `try-except` blocks to manage potential issues such as:
          * `speech_recognition.UnknownValueError`: When speech cannot be understood.
          * `speech_recognition.RequestError`: Problems connecting to the Google Speech Recognition service (e.g., network issues).
          * Exceptions during Gemini API communication.
      * Provides verbal feedback to the user when errors occur, enhancing the user experience and indicating what might be wrong.

-----

## Architecture Overview

The robot's architecture follows a classic client-server model for AI interaction, combined with direct hardware control:

```
+-------------------+      +---------------------+      +---------------------+
|    User (Voice)   |----->| USB Microphone      |----->| Raspberry Pi        |
+-------------------+      +---------------------+      | (Python Script)     |
                                                         |                     |
+-------------------+      +---------------------+      |   Speech-to-Text    |<---- Google Web Speech API
|   Speaker (Audio) |<-----| gTTS (Text-to-Speech)|<----|   (speech_recognition)|
+-------------------+      +---------------------+      |                     |
                                                         |   Command Parser    |
                                                         |                     |
                                                         |   GPIO Control      |----->| L298N Motor Driver |----->| DC Motors |
                                                         |                     |      +--------------------+      +-----------+
                                                         |   Google Gemini API |<---- | Google Gemini Cloud AI      |
                                                         |   Integration       |      | (gemini-2.5-flash)  |
                                                         +---------------------+      +-----------------------------+
```

1.  **Voice Input->** The user speaks commands into the **USB Microphone**.
2.  **Speech-to-Text (STT)->** The `speech_recognition` library on the **Raspberry Pi** captures the audio and sends it to the Google Web Speech API for transcription into text.
3.  **Command Parsing->** The Python script on the Raspberry Pi receives the transcribed text. It first attempts to match the text against predefined **robot movement commands**.
4.  **Movement Execution->** If a movement command is detected, the script directly controls the **DC motors** via the **L298N Motor Driver** by manipulating **GPIO pins**.
5.  **AI Interaction->** If the command is not a movement instruction, the text is forwarded to the **Google Gemini Cloud AI** via the `google-generativeai` library. Gemini processes the query, leveraging its vast knowledge and conversational capabilities.
6.  **AI Response->** Gemini sends back a text response to the Raspberry Pi.
7.  **Text-to-Speech (TTS)->** The `gTTS` library converts Gemini's text response (or the robot's pre-defined movement confirmations) into an MP3 audio file.
8.  **Audio Output->** The `mpg321` player plays the MP3 file through the **Speaker**, allowing the robot to vocalize its reply.

-----

## Components

A detailed breakdown of all hardware and software components used in this project.

### Hardware Components

| Component                   | Description                                                                                                                                                                                                                                           |
| :-------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Raspberry Pi 4 Model B** | The central processing unit. Its multiple GPIO pins, USB ports, Wi-Fi, and processing power make it ideal for robotics and AI tasks.                                                                                                                   |
| **L298N Motor Driver Module** | An H-bridge dual motor driver module. It allows the Raspberry Pi to control the speed and direction of two DC motors that require more current than the Pi's GPIO pins can provide. It handles voltages from 5V to 35V and currents up to 2A per motor. |
| **DC Motors (TT Motors)** | Two geared DC motors. These are inexpensive, robust, and commonly used in hobby robotics. The integrated gearbox reduces speed and increases torque, making them suitable for driving wheels. Each motor typically has two terminals.                     |
| **USB Microphone** | A plug-and-play microphone that connects directly to a USB port on the Raspberry Pi. It digitizes analog sound waves (your voice) into data the Pi can process.                                                                                        |
| **USB Sound Card** | *(Alternative to USB Microphone)* If using a 3.5mm microphone, this adapter converts USB to a 3.5mm audio input/output, acting as an external sound card for the Pi.                                                                                   |
| **3.5mm Microphone** | *(Alternative)* A standard analog microphone that can be used with a USB Sound Card.                                                                                                                                                                   |
| **Bluetooth Microphone** | *(Advanced Alternative)* A wireless microphone that connects via Bluetooth. Requires more complex software configuration on the Raspberry Pi (PulseAudio/ALSA setup for Bluetooth audio input).                                                           |
| **Speaker (USB/3.5mm)** | An audio output device. A USB speaker is plug-and-play, while a 3.5mm speaker connects to the Pi's audio jack (or the USB sound card's output). Essential for the robot to speak.                                                                        |
| **MicroSD Card** | The primary storage for the Raspberry Pi OS and all project files. A Class 10 or U1 (or faster) card (32GB or 64GB recommended) ensures good read/write speeds for optimal system performance.                                                              |
| **Power Bank (5V, 3A+)** | A portable battery pack for powering the Raspberry Pi. A minimum output of 3A is recommended for the Raspberry Pi 4 to ensure stable operation, especially with USB peripherals.                                                                          |
| **Motor Power Source** | A separate battery pack (e.g., 4xAA batteries for 6V, or a 7.4V/11.1V Li-ion battery) to power the motors via the L298N. This isolates the motors' power draw from the Raspberry Pi, preventing voltage drops and ensuring stable operation.              |
| **Jumper Wires** | Used for making electrical connections between the Raspberry Pi's GPIO pins, the L298N motor driver, and other components. A variety of Male-to-Female, Male-to-Male, and Female-to-Female wires are useful.                                             |
| **Robot Chassis** | (Optional) A structural frame to mount all components. Can be made from cardboard, wood, 3D-printed parts, or a dedicated robotics chassis kit.                                                                                                        |
| **Wheels** | Two wheels suitable for your chosen DC motors and chassis.                                                                                                                                                                                            |

### Software Components

| Component                         | Description                                                                                                                                                                                                                                                                     |
| :-------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Raspberry Pi OS (64-bit)** | The recommended operating system. A lightweight version (Lite) is sufficient, or the Desktop version if you prefer a graphical interface.                                                                                                                                          |
| **Python 3** | The programming language for the robot's core logic. The script is developed and executed using Python 3.                                                                                                                                                                       |
| **`RPi.GPIO`** | A Python library that provides an interface to control the GPIO pins on the Raspberry Pi. Essential for direct hardware interaction.                                                                                                                                              |
| **`speech_recognition`** | A Python library that wraps various speech recognition APIs (e.g., Google Web Speech API). It handles audio input from the microphone and sends it for transcription.                                                                                                             |
| **`gTTS` (Google Text-to-Speech)**| A Python library that interfaces with Google's Text-to-Speech API. It converts text strings into natural-sounding speech audio (MP3 format).                                                                                                                                      |
| **`google-generativeai`** | The official Python client library for Google's Generative AI models (Gemini). It allows the script to send user prompts to the Gemini model and receive text-based responses.                                                                                                    |
| **`mpg321`** | A command-line MP3 audio player. It's used by the script to play the `.mp3` files generated by `gTTS`. It's lightweight and efficient for embedded systems like the Raspberry Pi.                                                                                                 |
| **Google Gemini API Key** | A unique authentication key obtained from Google AI Studio. It authorizes your application to make requests to the Gemini API. Crucially, it's stored securely as an environment variable (`GOOGLE_API_KEY`) and accessed by the `google-generativeai` library.                 |
| **`systemd` (Optional)** | A system and service manager for Linux. It's used to configure the Python script to run automatically as a service when the Raspberry Pi boots up, ensuring continuous operation without manual intervention.                                                                     |

-----

![Screenshot2](https://makerselectronics.com/wp-content/uploads/2016/02/L298N-2A-Based-Motor-Driver-Module_2048x.webp)
![Screenshot2](https://hackster.imgix.net/uploads/attachments/981441/1_c1vv7b34H8WdvqeD_SBxgw.jpeg?auto=compress%2Cformat)
![Screenshot2](https://m.media-amazon.com/images/I/61axJ7V5-OL.jpg)
![Screenshot2](https://i.ytimg.com/vi/694YuORaXZY/mqdefault.jpg)
![Screenshot2](https://www.raspberrypi-spy.co.uk/wp-content/uploads/2019/05/pi_usb_sound_05.jpg)
![Screenshot2](https://i0.wp.com/iot4beginners.com/wp-content/uploads/2020/05/setup-1.png?resize=500%2C333&ssl=1)
![Screenshot2](https://cdn-shop.adafruit.com/970x728/3369-01.jpg)
![Screenshot2](https://m.media-amazon.com/images/I/61W6kFBTv7L._AC_SL1172_.jpg)

------




## Setup and Installation Guide

Follow these detailed steps to get our voice-controlled AI robot up and running ;)

<details>
<summary>1 Preparing the Raspberry Pi (Click to expand)</summary>
  
### 1\. Preparing the Raspberry Pi

1.  **Download Raspberry Pi OS:**
      * Go to the [official Raspberry Pi website](https://www.raspberrypi.com/software/operating-systems/) and download the Raspberry Pi Imager.
      * Use the Imager to flash **Raspberry Pi OS (64-bit)** onto your MicroSD card. For robotics projects, the "Lite" version is usually sufficient, but "Desktop" can be used if you prefer a graphical interface.
      * **Crucial Step:** During the flashing process with Raspberry Pi Imager, click the **gear icon (⚙️)** to configure advanced options:
          * **Set hostname:** (e.g., `raspberrypi-robot`).
          * **Enable SSH:** For remote access.
          * **Set username and password:** Remember these credentials.
          * **Configure wireless LAN:** Enter your Wi-Fi SSID and password to enable internet connectivity. This is vital for speech recognition and Gemini AI.
          * **Set locale settings:** Choose your country/region.
2.  **Initial Boot and Connection:**
      * Insert the MicroSD card into your Raspberry Pi.
      * Connect the Raspberry Pi to its power supply (Power Bank).
      * Wait a few minutes for the Pi to boot up.
      * Connect to your Raspberry Pi via SSH from your computer:
        ```bash
        ssh your_username@raspberrypi-robot.local # Replace your_username and hostname
        ```
        (If `.local` doesn't work, you might need to find the Pi's IP address from your router's connected devices list and use `ssh your_username@your_pi_ip_address`).
</details>

<details>
<summary>2 Installing Software Dependencies (Click to expand)</summary>

### 2\. Installing Software Dependencies

Once connected to your Raspberry Pi's terminal:

1.  **Update and Upgrade System:**
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```
    This ensures all existing packages are up-to-date.
2.  **Install `mpg321` (Audio Player):**
    ```bash
    sudo apt install mpg321 -y
    ```
    This package is essential for playing the audio responses.
3.  **Install Python Libraries:**
    ```bash
    pip install -q -U RPi.GPIO SpeechRecognition gTTS google-generativeai
    ```
      * `-q`: Quiet output, less verbose.
      * `-U`: Upgrade existing packages to their newest version.
      * These libraries provide all the necessary functionalities for GPIO control, speech recognition, text-to-speech, and Gemini API communication.
</details>

<details>
<summary>3 Google Gemini API Key Configuration (Click to expand)</summary>

### 3\. Google Gemini API Key Configuration

Your Gemini API key is sensitive information and should ideally not be hardcoded directly into your script. Storing it as an **environment variable** is a secure and recommended practice.

1.  **Obtain Your API Key:**
      * Navigate to [Google AI Studio](https://aistudio.google.com/).
      * Log in with your Google account.
      * Create a new API key if you don't have one already. Copy this key; you'll need it in the next step.
2.  **Set API Key as Environment Variable (for Systemd Service):**
      * If you plan to run your robot script as a background service (highly recommended for continuous operation and automatic startup), you need to define the environment variable within the `systemd` service file.
      * First, create your service file (e.g., `/etc/systemd/system/robot_voice_control.service`). We'll define the full service file in the "Running as a Service" section, but for now, the key part is adding the `Environment` line.
      * Open the file using `sudo nano` (or `vim`):
        ```bash
        sudo nano /etc/systemd/system/robot_voice_control.service
        ```
      * Inside the `[Service]` section, add the following line, replacing `YOUR_API_KEY_HERE` with your actual key:
        ```ini
        Environment="GOOGLE_API_KEY=YOUR_API_KEY_HERE"
        ```
      * Save the file (Ctrl+O, Enter) and exit (Ctrl+X).
      * After adding/modifying the service file, reload `systemd` to pick up the changes:
        ```bash
        sudo systemctl daemon-reload
        ```
3.  **Set API Key as Environment Variable (for Manual Testing):**
      * If you're testing the script manually in your current terminal session, you can set the environment variable like this:
        ```bash
        export GOOGLE_API_KEY="YOUR_API_KEY_HERE"
        ```
      * *Note:* This command only sets the variable for the current terminal session. It will be lost if you close the terminal.
</details>

<details>
<summary>4 Hardware Connections (Click to expand)</summary>

### 4\. Hardware Connections

**IMPORTANT: Always power off your Raspberry Pi and disconnect power from the L298N motor driver before making or changing any wiring connections to prevent damage to your components.**

#### **4.1. L298N Motor Driver to DC Motors:**

  * **Left Motor:** Connect its two terminals to the **OUT1** and **OUT2** screw terminals on the L298N.
  * **Right Motor:** Connect its two terminals to the **OUT3** and **OUT4** screw terminals on the L298N.
      * *Note:* The polarity here determines the default forward/backward direction. If a motor spins backward when it should go forward, simply reverse its two connections to the L298N.

#### **4.2. External Power Supply to L298N Motor Driver:**

  * Connect the **positive (+) terminal** of your dedicated motor power source (e.g., 6V battery pack, 7.4V Li-ion battery) to the **+12V (or VCC)** screw terminal on the L298N.
  * Connect the **negative (-) terminal** of your motor power source to the **GND** screw terminal on the L298N.
      * *Self-Correction Jumper:* The L298N often has a jumper near the `+5V` pin (sometimes labeled `5V EN`). This jumper, when in place, uses the motor power source to also provide 5V for the L298N's internal logic. For simplicity, leave it in place unless you need to power the L298N's logic from a separate 5V source (e.g., directly from the Pi's 5V pin, which is generally not recommended).

#### **4.3. Raspberry Pi to L298N Motor Driver (Control & Common Ground):**

Use Male-to-Female jumper wires for these connections.

  * **Common Ground (CRITICAL\!):** Connect a **GND pin** on your Raspberry Pi (e.g., Physical Pin 6) to the **GND pin** on the L298N. **This common ground is absolutely essential for signals to be correctly interpreted between the Pi and the driver.**
  * **Left Motor Control Pins (using BCM numbering for GPIO):**
      * `Raspberry Pi GPIO 27` (Physical Pin 13) to `IN1` on L298N
      * `Raspberry Pi GPIO 22` (Physical Pin 15) to `IN2` on L298N
      * `Raspberry Pi GPIO 17` (Physical Pin 11) to `ENA` on L298N (Enable for Motor 1)
  * **Right Motor Control Pins (using BCM numbering for GPIO):**
      * `Raspberry Pi GPIO 24` (Physical Pin 18) to `IN3` on L298N
      * `Raspberry Pi GPIO 25` (Physical Pin 22) to `IN4` on L298N
      * `Raspberry Pi GPIO 23` (Physical Pin 16) to `ENB` on L298N (Enable for Motor 2)

#### **4.4. USB Microphone & Speaker:**

  * **USB Microphone:** Plug it into any available USB 2.0 or USB 3.0 port on the Raspberry Pi.
  * **Speaker:**
      * If using a **USB Speaker**, plug it into another available USB port.
      * If using a **3.5mm Speaker**, plug it into the Raspberry Pi's 3.5mm audio jack.
  * **Configure Audio Devices:** After connecting, ensure the Raspberry Pi is using the correct audio input and output devices:
      * If you're using Raspberry Pi OS Desktop: Go to **Raspberry Pi icon (top-left)** -\> **Preferences** -\> **Audio Device Settings**. Select your USB Microphone as the **Input Device** and your Speaker as the **Output Device**.
      * If headless: You might need to edit ALSA configuration files or use command-line tools like `alsamixer` and `aplay -l` to identify and select the correct sound cards. Ensure your USB microphone is detected as `card 1` or similar, not `card 0` (which is typically the built-in audio output).

### 5\. Robot Python Code

Create a new file named `voice_robot.py` (or your preferred name) on your Raspberry Pi. For example, in your home directory (`/home/pi/`).

```bash
nano voice_robot.py
```

Then, copy and paste the entire Python code provided in the `voice_robot.py` INn these repo

-----
</details>

## Running the Robot

You have two main ways to run your robot script:

### 1\. Manual Execution (for Testing and Development)

To run the script directly from the terminal for testing purposes:

1.  Navigate to the directory where you saved `voice_robot.py`:
    ```bash
    cd /home/pi/ # Or wherever you saved it
    ```
2.  Ensure your `GOOGLE_API_KEY` environment variable is set for this session (as described in **Setup Step 3** under "Manual Testing").
3.  Execute the script:
    ```bash
    python3 voice_robot.py
    ```
    You will see print statements in the terminal indicating what the robot is doing, and you can listen for its verbal responses. To stop, press `Ctrl+C`.

### 2\. Running as a Systemd Service (Recommended for Deployment)

Running as a `systemd` service ensures your robot script starts automatically on boot and runs reliably in the background.

1.  **Create a Systemd Service File:**
    ```bash
    sudo nano /etc/systemd/system/robot_voice_control.service
    ```
2.  **Paste the following content** into the file. Make sure to replace `/home/pi/voice_robot.py` with the actual path to your script if it's different. Also, verify that `User=pi` is correct for your Raspberry Pi's user.
    ```ini
    [Unit]
    Description=Voice-Controlled AI Robot Service
    After=network.target sound.target

    [Service]
    ExecStart=/usr/bin/python3 /home/pi/voice_robot.py
    WorkingDirectory=/home/pi/
    StandardOutput=journal
    StandardError=journal
    Restart=always
    User=pi
    Environment="GOOGLE_API_KEY=YOUR_API_KEY_HERE" # <<< IMPORTANT: REPLACE WITH YOUR ACTUAL API KEY
    Environment="PULSE_SERVER=127.0.0.1" # Optional: if using PulseAudio/Bluetooth setup

    [Install]
    WantedBy=multi-user.target
    ```
      * `Description`: A brief description of your service.
      * `After`: Specifies that this service should start after the network and sound targets are ready.
      * `ExecStart`: The command to execute your Python script.
      * `WorkingDirectory`: Sets the working directory for your script.
      * `StandardOutput`/`StandardError`: Directs output/errors to the systemd journal for logging (viewable with `journalctl -u robot_voice_control.service`).
      * `Restart=always`: Ensures the service restarts if it crashes.
      * `User=pi`: Runs the script as the `pi` user.
      * `Environment="GOOGLE_API_KEY=..."`: **This is where your API key is securely defined for the service.** Make sure to replace `YOUR_API_KEY_HERE` with your actual key.
      * `Environment="PULSE_SERVER=127.0.0.1"`: This line is often helpful if you're experiencing issues with audio recognition or playback, especially in headless environments or with complex audio setups involving PulseAudio (common with Bluetooth microphones).
      * `WantedBy=multi-user.target`: Specifies that this service should be started when the system reaches the multi-user runlevel.
3.  **Save** (Ctrl+O, Enter) and **exit** (Ctrl+X).
4.  **Reload Systemd:**
    ```bash
    sudo systemctl daemon-reload
    ```
5.  **Enable the Service:**
    ```bash
    sudo systemctl enable robot_voice_control.service
    ```
    This configures the service to start automatically on boot.
6.  **Start the Service:**
    ```bash
    sudo systemctl start robot_voice_control.service
    ```
7.  **Check Service Status:**
    ```bash
    sudo systemctl status robot_voice_control.service
    ```
    You should see "active (running)".
8.  **View Logs:**
    ```bash
    journalctl -u robot_voice_control.service -f
    ```
    This command shows the real-time logs from your script, which is very useful for debugging.

-----

## Usage and Interaction

Once your robot is running, you can interact with it through voice:

1.  **Initial Greeting:** The robot will greet you with "Hello, Engineer. I am your robot. I am now connected to Google's artificial intelligence. How can I assist you today?"
2.  **Give Movement Commands:** Speak clearly when giving commands.
      * **Forward:** "امشي لقدام", "اتحرك للامام", "تقدم", "Forward", "Go forward"
      * **Backward:** "ارجع لورا", "تحرك للخلف", "Backward", "Go backward"
      * **Turn Right:** "لف يمين", "اتجه يمين", "Turn right"
      * **Turn Left:** "لف شمال", "اتجه يسار", "Turn left"
      * **Stop:** "وقف", "توقف", "ستوب", "Stop"
      * The robot will confirm the action and perform it for a short duration, then stop.
3.  **Engage in Conversation with AI:** For anything not related to movement, simply ask a question or make a statement. The robot will send your speech to Gemini and vocalize Gemini's response.
      * Examples:
          * "What is the capital of France?"
          * "Can you tell me a joke?"
          * "Summarize the plot of Romeo and Juliet."
          * "What is the latest news about AI?"
          * "هل يمكنك ان تخبرني قصة قصيرة؟" (Can you tell me a short story?
          * "ما هو طقس اليوم في الإسكندرية؟" (What is the weather like today in Alexandria?)
          * *Follow-up:* "What about tomorrow?" (Gemini will remember the previous context).
4.  **Exit the Program:** To stop the robot and exit the script, say:
      * "Exit", "Quit", "اخرج"
      * The robot will respond with "Goodbye, Engineer. Powering down." and gracefully shut down.

-----


## Contributing and Future Enhancements

This project is a solid foundation, but there's always room for improvement and new features\! Contributions, bug reports, and feature requests are welcome ^_^

### Potential Enhancements:
- Integrate ultrasonic sensors (HC-SR04) or infrared sensors to enable the robot to detect and avoid obstacles.
- Add line tracking sensors to allow the robot to follow a designated path.
- Implement Proportional-Integral-Derivative (PID) control for smoother and more precise motor speed and steering.
  
-------

- Add a Raspberry Pi Camera Module for computer vision tasks:
          * Object detection (identifying faces, specific items).
          * Facial recognition.
          * Following a colored object.
- Integrate sensors for temperature, humidity, light, etc., and have the robot report environmental data.
  
-------

- Implement a "wake word" ("Hey Robot," "Hello Pi") using libraries like Picovoice (Porcupine), Mycroft Precise, or Snowboy. This would make the robot only listen for commands after the wake word is detected, saving processing power and preventing accidental activations.
  
-------

- Develop a simple web-based interface (using Flask) to monitor robot status, view logs, or send commands from a browser.
- Add LEDs to indicate listening status, processing, or errors.

-------

- Implement low-battery detection and provide verbal warnings.
- Consider more efficient motor control with PWM for variable speed control instead of just full speed.
 
-------

- Try to infer user emotion from voice (complex, but possible with specialized libraries).
- If a camera is added, explore Gemini's multimodal capabilities to have the robot "see" and describe its environment.

-----


## Troubleshooting Guide

Here's a more detailed troubleshooting guide for common issues you might encounter:

### 1\. **Audio Input/Output Issues (Microphone/Speaker)**

  * **"Could not understand audio" / Robot doesn't respond to voice:**
      * **Microphone Connection:** Ensure the USB microphone (or USB sound card) is firmly plugged into a USB port on the Raspberry Pi.
      * **Microphone Selection:**
          * **Desktop OS:** Go to `Raspberry Pi Icon` \> `Preferences` \> `Audio Device Settings`. Under the `Input` tab, ensure your USB microphone is selected (it might appear as "USB Audio Device" or its specific model name). Adjust the input volume if available.
          * **Headless OS:** Use `arecord -l` to list available sound cards and their device numbers. Your USB microphone will likely be `card 1` or higher. You may need to create/edit `/etc/asound.conf` or `~/.asoundrc` to set the default input device.
          * **Test Microphone:**
            ```bash
            arecord -D plughw:1,0 -f S16_LE -c1 -r44100 -d5 test.wav # Replace '1,0' with your mic's card,device
            ```
            Speak into the mic for 5 seconds. Then play it back: `aplay test.wav`. If you hear your voice, the mic setup is correct.
      * **Ambient Noise:** Ensure the room isn't too noisy. The `r.adjust_for_ambient_noise(source)` line helps, but excessive noise can still cause problems.
      * **Microphone Quality:** Some cheaper microphones might have poor quality.
  * **"Robot cannot speak at the moment" / No audio output:**
      * **Speaker Connection:** Ensure your speaker is correctly connected (USB or 3.5mm jack).
      * **Speaker Selection:**
          * **Desktop OS:** In `Audio Device Settings` (same as above), ensure your speaker is selected as the **Output Device**.
          * **Headless OS:** Use `aplay -l` to list sound cards. The default output is usually `card 0` (Pi's built-in). If using a USB speaker, it might be `card 1`. You might need to configure ALSA for the correct output device.
          * **Test Speaker:**
            ```bash
            aplay /usr/share/sounds/alsa/Front_Center.wav # Play a test sound
            ```
      * **`mpg321` Installation:** Confirm `mpg321` is installed (`sudo apt install mpg321`).
      * **Volume:** Check system volume levels (`alsamixer` in terminal for headless, or desktop volume controls).

### 2\. **Robot Movement Issues (Motors)**

  * **Robot not moving at all / Motors not spinning:**
      * **Power Supply to L298N:** Verify the separate battery pack for the motors is connected to the `+12V` (or `VCC`) and `GND` terminals of the L298N, and that the batteries are charged.
      * **Common Ground:** **Crucial\!** Ensure a `GND` pin from the Raspberry Pi is connected to the `GND` pin of the L298N. Without a common ground, control signals cannot be properly exchanged.
      * **Motor Connections:** Check that the motor wires are securely connected to `OUT1/OUT2` and `OUT3/OUT4` on the L298N.
      * **GPIO Pin Correctness:** Double-check your jumper wire connections against the specified GPIO BCM numbers (17, 22, 23, 24, 25, 27) and the L298N inputs (`IN1`, `IN2`, `ENA`, `IN3`, `IN4`, `ENB`).
      * **`ENA`/`ENB` Pins:** Ensure the `ENA` and `ENB` pins on the L298N are correctly connected to GPIO pins and that their corresponding GPIO outputs are set to `GPIO.HIGH` in the `setup()` function. If they are `LOW`, the motors will not activate.
      * **Motor Health:** Test motors directly with a battery (briefly\!) to ensure they work.
  * **Motors spinning in the wrong direction:**
      * If a motor spins backward when it should go forward, simply swap its two wires connected to the L298N output terminals (e.g., swap OUT1 and OUT2 for the left motor).
  * **Motors making noise but not moving:**
      * **Insufficient Power:** The motor power supply might be too weak or batteries are low.
      * **Overload:** The robot chassis might be too heavy, or there's too much friction.
      * **Motor Driver Capacity:** Ensure your motors' current draw doesn't exceed the L298N's capacity (2A per motor).

### 3\. **AI/API Communication Issues**

  * **"Sorry, I cannot connect to the AI at the moment" / No Gemini response:**
      * **Internet Connection:** Your Raspberry Pi *must* have an active internet connection to reach Google's services. Test by pinging google: `ping google.com`.
      * **Google Gemini API Key:**
          * Verify your `GOOGLE_API_KEY` is correct and active.
          * Ensure it's set as an environment variable in the correct context (either `export` for manual run, or in the `systemd` service file). Even a single typo will prevent access.
      * **API Usage Limits:** While less common for hobby projects, check the Google AI Studio dashboard for your API key's usage and any potential limits you might have hit.
      * **Firewall:** Ensure no firewall on your network or Pi is blocking outgoing connections to Google's API endpoints.
  * **`google.generativeai.types.client_types.APITimeoutError` or similar:**
      * Indicates a network timeout. Check internet connection stability.
      * Could be a temporary issue with Google's servers. Try again after a short while.

### 4\. **Script Execution Issues**

  * **`ModuleNotFoundError: No module named 'RPi.GPIO'` (or other libraries):**
      * You forgot to install one or more of the Python libraries. Go back to **Setup Step 2** and run the `pip install` command.
  * **`PermissionError: [Errno 13] Permission denied: '/dev/gpiomem'`:**
      * This typically means the user running the script (`pi` user by default) doesn't have permissions to access GPIO. Ensure your user is part of the `gpio` group:
        ```bash
        sudo adduser pi gpio
        sudo reboot
        ```
  * **Script not starting on boot (if using systemd):**
      * Check `sudo systemctl status robot_voice_control.service` for errors.
      * Ensure the `ExecStart` path in your service file is absolutely correct (`/usr/bin/python3 /home/pi/voice_robot.py`).
      * Confirm `sudo systemctl enable robot_voice_control.service` was run successfully.
      * Review `journalctl -u robot_voice_control.service` for any startup errors.

-----
