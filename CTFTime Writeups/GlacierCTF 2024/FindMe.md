---
title: FindMe
author: Josan George
date: 2024-11-24
categories: [GlacierCTF]
tags: [misc,pdf]
---

![](https://miro.medium.com/v2/resize:fit:543/1*5243Lk438-rkAzZ23B8cYQ.png)

Title and Description of the Challenge

# **Introduction:**

In the thrilling world of Capture The Flag (CTF) challenges, “FindMe” from **GlacierCTF 2024** stood out as an intriguing steganography problem in the Miscellaneous category. The challenge posed a seemingly simple scenario:

“A friend sent me this PDF, but I don’t know what he wants me to do with it.”

This description hinted at hidden data within the provided PDF file. Here’s how I approached and solved this challenge, step by step.

# Challenge Details

**Category**: Miscellaneous  
**Challenge Name**: FindMe

The challenge provided a PDF file (`chall.pdf`). Suspecting steganography, I began my exploration.

![](https://miro.medium.com/v2/resize:fit:770/1*T4PoeIemow7Z-PecjPJdZA.png)

Overview of the PDF

# Tools and Research

After some online research, I found that the tool **peepdf** is a powerful utility for analyzing and extracting content from PDF files, especially in steganography-related tasks.

## Useful References:

1. [peepdf GitHub Repository](https://github.com/jesparza/peepdf)
2. [peepdf BlackHat Documentation (PDF)](https://www.blackhat.com/docs/eu-15/materials/eu-15-Esparza-peepdf.pdf)
3. [Medium Guide to Investigating Malicious PDFs](https://tho-le.medium.com/investigate-malicious-pdf-documents-with-mpeepdf-a-quick-user-guide-b7f31b3b013)

# Step-by-Step Solution:

## Step 1: Initial Analysis with peepdf

I started by running **peepdf** in interactive mode to analyze the contents of the PDF file:

peepdf -i chall.pdf

![](https://miro.medium.com/v2/resize:fit:770/1*vceMfAY4LS6mLk57zgMWAw.png)

Running peepdf as interactive mode

Upon `tree` command, I noticed streams numbered **20** and **743**. Streams in a PDF file often contain objects like images or embedded data.

![](https://miro.medium.com/v2/resize:fit:696/1*NZMxnkb7kopUyUf8MTBtEA.png)

## Step 2: Confirming Streams

I used the `stream` command to verify the contents of streams 20 and 743:

stream 20    
stream 743

![](https://miro.medium.com/v2/resize:fit:770/1*TtMTFs7tRWzKWsxc5aR6CA.png)

Output of the stream command

The output confirmed that a PNG image started at stream 20 and ended at stream 743. This was a clear indicator of hidden content.

## Step 3: Automating PNG Extraction

To extract the PNG, I crafted a command list to automate the process using **peepdf**.

![](https://miro.medium.com/v2/resize:fit:607/1*q3ftspp7K3inlTnlsVIlOw.png)

command list

Key commands included:

- `>`: Extracts the object to a file.
- `>>`: Appends extracted content to a file.

Here’s the script I used to extract the PNG:

import subprocess  
  
# Define the command to start peepdf in interactive mode  
peepdf_command = ["peepdf", "-i", "chall.pdf"]  
  
# Open the output file containing commands  
with open("output.txt", "r") as file:  
    commands = file.readlines()  
  
# Start the peepdf process in interactive mode  
process = subprocess.Popen(peepdf_command, stdin=subprocess.PIPE, stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)  
  
try:  
    # Send each command from the file to the interactive session  
    for command in commands:  
        command = command.strip()  # Remove any extra whitespace or newline characters  
        print(f"Running: {command}")  
        process.stdin.write(command + "\n")  # Send the command to the interactive shell  
        process.stdin.flush()  # Ensure the command is sent immediately  
        # Optionally, capture and display output  
        output = process.stdout.readline()  
        print(output.strip())  # Print the response from the interactive shell  
except Exception as e:  
    print(f"An error occurred: {e}")  
finally:  
    # Close the interactive session  
    process.stdin.write("exit\n")  # Ensure you exit peepdf cleanly  
    process.stdin.flush()  
    process.terminate()

![](https://miro.medium.com/v2/resize:fit:667/1*u1gTHyLSRG-ayD0xR86NPQ.png)

Running the Extraction Script

## Step 4: Opening the Extracted PNG

When I tried to open the extracted PNG, it initially threw errors. Suspecting some corruption, I decided to explore further.

![](https://miro.medium.com/v2/resize:fit:597/1*uUbTnNxNTwFXLfLZZnW-Fw.png)

Opening the png file

## Step 5: Decoding in CyberChef

Using [CyberChef](https://gchq.github.io/CyberChef/), I uploaded the PNG file. CyberChef’s **Magic** tool provided a glimpse of the hidden image. After applying necessary decodings and transformations, I finally revealed the complete image.

![](https://miro.medium.com/v2/resize:fit:770/1*fo47Ao4x4ZIV9TUYqfVDMw.png)

Uploading the file to CyberChef

## Step 6: Discovering the Flag

The extracted image contained the flag prominently displayed. Here’s the flag:

![](https://miro.medium.com/v2/resize:fit:770/1*1oZEYFQTf9p57nCrIUJFNg.png)

flag

gctf{0934_https://www.youtube.com/watch?v=fozyNJuasgA_0384}

# Key Takeaways

1. **PDF Analysis Tools**: Tools like **peepdf** are invaluable for analyzing and extracting hidden content in PDF files.
2. **Automation in CTFs**: Automating repetitive tasks, such as extracting streams, saves time and ensures accuracy.
3. **Decoding Tools**: Tools like **CyberChef** are versatile and can handle a wide range of data decoding challenges.

# References

- [peepdf GitHub Repository](https://github.com/jesparza/peepdf)
- [peepdf BlackHat Documentation (PDF)](https://www.blackhat.com/docs/eu-15/materials/eu-15-Esparza-peepdf.pdf)
- [Medium Guide to Investigating Malicious PDFs](https://tho-le.medium.com/investigate-malicious-pdf-documents-with-mpeepdf-a-quick-user-guide-b7f31b3b013)

Follow me on Linkedin — [Josan George](https://www.linkedin.com/in/josan-george-a86370227/)
