# Linux-File-IO-Systems-locking
Ex07-Linux File-IO Systems-locking
# AIM:
To Write a C program that illustrates files copying and locking

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux IO Systems locking

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:

## 1.To Write a C program that illustrates files copying 

```c
#include <unistd.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdlib.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    if (argc != 3) {
        fprintf(stderr, "Usage: %s <source_file> <destination_file>\n", argv[0]);
        exit(EXIT_FAILURE);
    }

    char block[1024];
    int in, out;
    ssize_t nread;

    // Open source file
    in = open(argv[1], O_RDONLY);
    if (in == -1) {
        perror("Error opening source file");
        exit(EXIT_FAILURE);
    }

    // Open destination file
    out = open(argv[2], O_WRONLY | O_CREAT | O_TRUNC, S_IRUSR | S_IWUSR);
    if (out == -1) {
        perror("Error opening destination file");
        close(in);
        exit(EXIT_FAILURE);
    }

    // Copy contents
    while ((nread = read(in, block, sizeof(block))) > 0) {
        if (write(out, block, nread) != nread) {
            perror("Error writing to destination file");
            close(in);
            close(out);
            exit(EXIT_FAILURE);
        }
    }

    if (nread == -1) {
        perror("Error reading source file");
    }

    close(in);
    close(out);

    return EXIT_SUCCESS;
}




```





## 2.To Write a C program that illustrates files locking
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/file.h>

void display_lslocks() {
    printf("\nCurrent `lslocks` output:\n");
    fflush(stdout);
    system("lslocks");
}

int main(int argc, char *argv[]) {
    if (argc < 2) {
        fprintf(stderr, "Usage: %s <filename>\n", argv[0]);
        exit(EXIT_FAILURE);
    }

    char *file = argv[1];
    int fd;

    printf("Opening %s\n", file);

    fd = open(file, O_WRONLY);
    if (fd == -1) {
        perror("Error opening file");
        exit(EXIT_FAILURE);
    }

    if (flock(fd, LOCK_SH) == -1) {
        perror("Error acquiring shared lock");
        close(fd);
        exit(EXIT_FAILURE);
    }
    printf("Acquired shared lock using flock\n");
    display_lslocks();

    sleep(1);

    if (flock(fd, LOCK_EX | LOCK_NB) == -1) {
        perror("Error upgrading to exclusive lock");
        flock(fd, LOCK_UN);
        close(fd);
        exit(EXIT_FAILURE);
    }
    printf("Acquired exclusive lock using flock\n");
    display_lslocks();

    sleep(1);

    if (flock(fd, LOCK_UN) == -1) {
        perror("Error unlocking");
        close(fd);
        exit(EXIT_FAILURE);
    }
    printf("Unlocked\n");
    display_lslocks();

    close(fd);
    return 0;
}



```



## OUTPUT

C program that illustrates files copying

<img width="803" height="659" alt="Screenshot at 2025-10-22 14-01-17" src="https://github.com/user-attachments/assets/1fe43da7-4d10-4b60-923d-b1acb242cc73" />




C program that illustrates files locking

<img width="1920" height="1057" alt="Screenshot at 2025-10-22 14-11-53" src="https://github.com/user-attachments/assets/186790ec-3e65-4ba6-a16a-c6bb58dbde58" />




# RESULT:
The programs are executed successfully.
