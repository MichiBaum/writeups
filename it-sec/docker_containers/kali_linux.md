# Kali Linux Docker Container


    docker pull kalilinux/kali-rolling
    docker run -d --tty --interactive --privileged --name kali_box -d kalilinux/kali-rolling
    docker attach kali_box

    apt update && apt -y install kali-linux-headless