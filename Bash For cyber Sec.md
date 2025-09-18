You can get a rough idea of what operating system is running on another machine by pinging it, and
looking at the Time-to-Live (TTL) figure in the response. Here’s how it works:
• 64: If the TTL of a ping response is 64, then the operating system of the target machine is Linux,
some sort of BSD, or macOS.
• 128: A 128 TTL indicates that the target machine is running Windows.
• 255: This indicates that the target machine is running either Solaris or a Solaris clone, such
as OpenIndiana


# Simple Port Scan 

`cat< /dev/tcp/ip/port`

not using bash shell?
bash -c 'cat < /dev/tcp/time.nist.gov/13'
or curl ip:port 

# self note
you can tell if a port is opne by how fast the the command responds
