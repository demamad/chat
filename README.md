# chat


#!/usr/bin/python3

import socket
import sys
import select
HOST = ''
PORT = 7777
try:
    clients_socket = []
    list_nicks =[]
    socket_serveur = socket.socket(socket.AF_INET6, socket.SOCK_STREAM)
    socket_serveur.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    socket_serveur.bind(("", 7777))
    socket_serveur.listen(10)
    print("Le serveur ecoute a present sur le port")
    clients_socket.append(socket_serveur)
    while True:
        read_socket, [], [] = select.select(clients_socket, [], [])
        for l in read_socket:
            if l == socket_serveur:
                (client, adress) = socket_serveur.accept()
                print("Connecté : " + str(adress))
                clients_socket.append(client)
            else:
                data = client.recv(1024)
                if len(data) == 0 or data == b" " or data == "\n":
                    clients_socket.remove(client)
                    client.close()
                    break
                elif data == "MSG":
                    for x in read_socket:
			            if x!=l and x!= socket_serveur:
				            x.sendall(data)
                        else:
                            client.sendall(b'invalid command\n')
    socket_serveur.close()

except Exception as my_exc:
    print("\nERROR : " + str(my_exc))
    socket_serveur.close()
