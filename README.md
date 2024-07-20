# server side:
import threading
import socket
host ="127.0.0.1" #local host
port=50000
server=socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind((host,port))
server.listen()

clients=[]
nicknames=[]

def broadcast(message):
    for client in clients:
        client.send(message)

def handle_client(client):
   while True:
       try:
           data = client.recv(1024)
           broadcast(data)
       except:
           index=clients.index(client)
           clients.remove(client)
           client.close()
           nickname=nicknames[index]
           nicknames.remove(nickname)
           broadcast(f'{nickname}has left the chat'.encode('ascii'))
           break

def recieve():
    while True:
     client,address=server.accept()
     print(f"Connected with: {str(address)}")
     client.send('Nick'.encode('ascii'));
     nickname=client.recv(1024).decode('ascii')
     nicknames.append(nickname)
     clients.append(client)
     print(f"nickname of client is {nickname}")
     broadcast(f'{nickname} has joined the chat   '.encode('ascii'))
     client.send(f'connected to the server'.encode('ascii'))
     thread=threading.Thread(target=handle_client,args=(client,))
     thread.start()

print('server starting...')
recieve()

# client side:
import socket
import threading

nickname=input("choose Nickname:")

client=socket.socket(socket.AF_INET, socket.SOCK_STREAM)
client.connect(('127.0.0.1', 50000))
def recieve():
    while True:
        try:
            data = client.recv(1024).decode('ascii')
            if data=='Nick':
                client.send(nickname.encode('ascii'))
            else:
                print(data)
        except:
         print('An error occured')
         client.close()
         break;

def write():
       while True:
         message= f'{nickname}:{input("")}'
         client.send(message.encode('ascii'))

recieve_thread=threading.Thread(target=recieve)
recieve_thread.start()

write_thread=threading.Thread(target=write)
write_thread.start()

