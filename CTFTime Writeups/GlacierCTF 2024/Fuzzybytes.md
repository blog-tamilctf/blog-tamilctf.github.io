---
title: Fuzzybytes
author: Sibivasan
date: 2024-11-24
categories: [GlacierCTF]
tags: [web,zipslip]
---

# Glacier CTF-2024

Application is vulnerable to zipslip(tarslip) vulnerability to get the command injection and SUID privilege to get the flag.

## Fuzzybytes:

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252FJTs1HYEabxgAjK24ADUP%252Fimage.png%3Falt%3Dmedia%26token%3D09aa1f27-dbf3-4441-b011-3c29d8427342&width=768&dpr=4&quality=100&sign=ca1f8862&sv=1)

### Static analysys:

In `Upload.php` executing the `/check_for_malicious_code.py`file to check the uploaded file.

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252FCD9k5IqtS7iam6MfvyWB%252Fimage.png%3Falt%3Dmedia%26token%3D3ed44951-e386-4241-ab1c-b6d18e155b8c&width=768&dpr=4&quality=100&sign=1f5736d7&sv=1)

In `check_for_malicious_code.py:` application extracts the files from the `tar` and checking for any malicious content and after checking it will remove the file from the directory.

For Extracting the .tar application uses the `tar.extractall` and for removing `shutil.rmtree` once the file uploded and scanned it will deleted by the application.

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252F484PtlIvbZrQwCcQ94JA%252Fimage.png%3Falt%3Dmedia%26token%3D3cd88b45-6f66-421c-9e9f-5829173776df&width=768&dpr=4&quality=100&sign=2eabeaa7&sv=1)

`tar.extractall` fuction is vulnerable to `directory traversal attack`.

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252Fmvui7BSkcpDDzgzDjK2T%252Fimage.png%3Falt%3Dmedia%26token%3Daf50ca74-a629-4713-b889-154e4c4df2d9&width=768&dpr=4&quality=100&sign=edd7eb5&sv=1)

POC:

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252FBKczhQF72EM3kkquASJf%252Fimage.png%3Falt%3Dmedia%26token%3D105f029d-1a2e-41df-99a1-352ca6535e28&width=768&dpr=4&quality=100&sign=501ce8b2&sv=1)

### Dynamic analysys:

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252FywhgY5RuTc0ZFZreAzbX%252Fimage.png%3Falt%3Dmedia%26token%3D554d36ef-f367-49f2-8660-d749dcfc1675&width=768&dpr=4&quality=100&sign=e8562a7&sv=1)

Create the tar file:

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252FnuyxiAII4MmyoSKr8E0z%252Fimage.png%3Falt%3Dmedia%26token%3D315f40da-1482-42fd-8186-a143655aa927&width=768&dpr=4&quality=100&sign=51f35834&sv=1)

Try our payload in local environment:

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252FqLKip8kBm8EKvduQE5GS%252Fimage.png%3Falt%3Dmedia%26token%3D9cb64117-c1f8-4a23-9455-5d0fe4e079eb&width=768&dpr=4&quality=100&sign=34bf62c7&sv=1)

We have uploaded the file:

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252FfejFqwqaYgUN7gyXe5XP%252Fimage.png%3Falt%3Dmedia%26token%3De05222e2-79ff-47fa-b53a-16699ed71fc7&width=768&dpr=4&quality=100&sign=a60ff3d1&sv=1)

We have successfully traverse and put it in `/var/www/html` repository.

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252Fc1cGznhxBXTbqRQsllWX%252Fimage.png%3Falt%3Dmedia%26token%3D897b144e-4cde-4f22-81d2-f51f584764c6&width=768&dpr=4&quality=100&sign=f04337ee&sv=1)

Successfully got a command execution:

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252FeG9vVOPA2sdJDc6HGmX4%252Fimage.png%3Falt%3Dmedia%26token%3Df1722350-5997-452e-94a3-6f50625b42e7&width=768&dpr=4&quality=100&sign=303b92d0&sv=1)

But not able to read the flag.txt.

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252F6snLhJ4zZgWmQBgnY4zv%252Fimage.png%3Falt%3Dmedia%26token%3Dff773361-6105-4685-a74e-4695298a55b6&width=768&dpr=4&quality=100&sign=334d9a92&sv=1)

We don't have permission to read the /root/flag.txt

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252FL1A8Gyp9ujii3pLk3Tq9%252Fimage.png%3Falt%3Dmedia%26token%3D8bef60ee-b8a2-4068-b2eb-520deb0d42d4&width=768&dpr=4&quality=100&sign=3e274590&sv=1)

While checking for the SUID binaries we got tar .

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252FWwL9YcXDXr5KwBDHtVsT%252Fimage.png%3Falt%3Dmedia%26token%3D2b2ed2a3-e0c1-4a12-8f92-ef08e72ebe4b&width=768&dpr=4&quality=100&sign=1383222f&sv=1)

So decided to zip the flag using the tar.

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252FpBE8HWPszk2WOJ1aOfSI%252Fimage.png%3Falt%3Dmedia%26token%3D5cab90d3-9b99-43c7-b813-66e4ca373acb&width=768&dpr=4&quality=100&sign=d14b537e&sv=1)

Uploaded the new payload and execute.

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252FzJiv0aBak8z6NSuOlmtl%252Fimage.png%3Falt%3Dmedia%26token%3Dc7d212df-1dc1-4c4d-82b5-a1da45ad6e8e&width=768&dpr=4&quality=100&sign=87a9e7c9&sv=1)

While checking the local directory we get the flag.tar

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252FmYRTgqujD7VMyChLUQ4w%252Fimage.png%3Falt%3Dmedia%26token%3D7486b8df-99a1-4f42-ab37-88135d456a34&width=768&dpr=4&quality=100&sign=cf0eb328&sv=1)

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252FhnpGch6RS8hqf9R5MBIc%252Fimage.png%3Falt%3Dmedia%26token%3D8e80760c-b7d9-4e46-8029-ffa1a5529f44&width=768&dpr=4&quality=100&sign=af60211a&sv=1)

Got the flag in local.

and got the flag in the CTF

![](https://sibivasan.gitbook.io/~gitbook/image?url=https%3A%2F%2F1152868336-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FCgUNmwmwzhhEXlNJ5H3g%252Fuploads%252F1ZHKUfWjhgOhZcXpFHMp%252Fimage.png%3Falt%3Dmedia%26token%3Dcb89b769-00fa-4170-bb85-a45dd297c528&width=768&dpr=4&quality=100&sign=37a8bc8e&sv=1)

Flag: `gctf{c0nGr4tZ_on_Z1p_sLiDinG_4nD_Tar_diving}`
