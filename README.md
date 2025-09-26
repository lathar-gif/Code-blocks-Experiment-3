# Code-blocks-Experiment-3
STUDY OF SOCKET PROGRAMMING AND CLIENT SERVER MODEL
# Socket Programming: Client-Server Data Transfer

## 🎯 AIM
To write a Socket program to transfer data between Client and Server.

## 🧰 EQUIPMENTS REQUIRED
- PC with Linux operating system

## 🛠️ PROCEDURE
1. Connect two computers in Wired/Wireless LAN.
2. Ensure both computers are on the same network and can ping each other.
3. Open terminal and type `gedit filename.c` to write the program.
4. Compile using `gcc filename.c`.
5. Execute using `./a.out`.
6. Run the program on both server and client machines.
7. Enter the IP address of the server on the client machine.
8. Enter the data to be transferred and verify its size.

## 💻 PROGRAM

### 🔸 SERVER

```c
/*
C socket server example
*/
#include <stdio.h>
#include <string.h>    //strlen
#include <sys/socket.h>
#include <arpa/inet.h> //inet_addr
#include <unistd.h>    //write

int main(int argc , char *argv[]) {
    int socket_desc , client_sock , c , read_size;
    struct sockaddr_in server , client;
    char client_message[2000];

    // Create socket
    socket_desc = socket(AF_INET , SOCK_STREAM , 0);
    if(socket_desc == -1) {
        printf("Could not create socket");
    }
    puts("Socket created");

    // Prepare the sockaddr_in structure
    server.sin_family = AF_INET;
    server.sin_addr.s_addr = INADDR_ANY;
    server.sin_port = htons(8888);

    // Bind
    if(bind(socket_desc, (struct sockaddr *)&server , sizeof(server)) < 0) {
        perror("bind failed. Error");
        return 1;
    }
    puts("Bind done");

    // Listen
    listen(socket_desc , 3);

    // Accept incoming connection
    puts("Waiting for incoming connections...");
    c = sizeof(struct sockaddr_in);
    client_sock = accept(socket_desc, (struct sockaddr *)&client, (socklen_t*)&c);
    if(client_sock < 0) {
        perror("accept failed");
        return 1;
    }
    puts("Connection accepted");

    // Receive message from client
    while((read_size = recv(client_sock , client_message , 2000 , 0)) > 0) {
        // Echo message back to client
        write(client_sock , client_message , strlen(client_message));
    }

    if(read_size == 0) {
        puts("Client disconnected");
        fflush(stdout);
    } else if(read_size == -1) {
        perror("recv failed");
    }

    return 0;
}
/*
C ECHO client example using sockets
*/
#include <stdio.h>      //printf
#include <string.h>     //strlen
#include <sys/socket.h> //socket
#include <arpa/inet.h>  //inet_addr
#include <unistd.h>     //close

int main(int argc , char *argv[]) {
    int sock;
    struct sockaddr_in server;
    char message[1000], server_reply[2000];

    // Create socket
    sock = socket(AF_INET , SOCK_STREAM , 0);
    if(sock == -1) {
        printf("Could not create socket");
    }
    puts("Socket created");

    server.sin_addr.s_addr = inet_addr("127.0.0.1");
    server.sin_family = AF_INET;
    server.sin_port = htons(8888);

    // Connect to remote server
    if(connect(sock , (struct sockaddr *)&server , sizeof(server)) < 0) {
        perror("connect failed. Error");
        return 1;
    }
    puts("Connected");

    while(1) {
        printf("Enter message: ");
        scanf("%s", message);

        // Send data
        if(send(sock , message , strlen(message) , 0) < 0) {
            puts("Send failed");
            return 1;
        }

        // Receive reply
        if(recv(sock , server_reply , 2000 , 0) < 0) {
            puts("recv failed");
            break;
        }

        puts("Server reply:");
        puts(server_reply);
    }

    close(sock);
    return 0;
}

## 🎯 RESULT
Thus, a socket program was successfully written to transfer data between client and server, and its performance was studied.
