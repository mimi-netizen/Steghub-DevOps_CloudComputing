                                        SDLC

The software-development life-cycle (SDLC) is used to facilitate the development of a large software product in a systematic, well-defined, and cost-effective way.  
Some reasons for using a life-cycle model include:  
-Understanding an entire process  
-Ensures a structured approach to development.  
-Helps in planning of resources in advance.  
-Helps in keeping track of progress

![My Image](https://media.geeksforgeeks.org/wp-content/uploads/20231220113035/SDLC.jpg)

                                     LAMP STACK

<span style="color: blue">LAMP</span> Stack is a software development stack comprising of Linux, Apache, MySQL and PHP.
The LAMP stack is used for building and delivering web-based applications, such as:  
-Content Management Systems - CMS platforms, like WordPress, are developed using PHP.  
-Ecommerce platforms - Relational databases, like MySQL, are a robust backend solution that can efficiently handle transactions, extensive product catalogs, and vast amounts of user data.  
-Dynamic Websites that need to adjust content based on user interactions in real-time benefit from PHP's backend scripting capabilities.  
-Data-driven applications benefit from the stack's ability to process data and complex data structures in real-time.

![My image](https://phoenixnap.com/kb/wp-content/uploads/2022/01/visual-representation-of-the-lamp-stack-pnap.png)

                                   CHOWN & CHMOD

chown defines who owns the file and is also used to change the ownership of a file, example

                               ls -l file
                               -rwxrwxr-x  2  celyne steghub   5 May  9 12:49 file

Here celyne is the owner of the file and steghub is the group. We can use: sudo chown victor:sudo file to change the owner of the file to victor and the group to sudo; Now the file belongs to "victor" and everyone in "sudo" group.

However with chmod we define who can do what? who has the right to read a file, write to a file or execute it, example:

                              chmod 777 file

gives the rights of read, write and execute to everyone including owner, group and everyones else.

![My image](https://i.stack.imgur.com/6pdvJm.png)

                                     TCP & UDP

TCP (Transmission Control Protocol) and UDP (User Datagram Protocol) are both fundamental protocols in the Transport Layer of the TCP/IP suite, which is responsible for reliable communication between applications on different devices over a network although they differ in their approach to data transmission. Below are some of the differences:

![My image](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi3Pa2R7Sv_vYfxTsfv13Tmn4j8e3MCK28VpMFvAd7rFAWeE5NfxQGDut9NGIYa6_sT23pybRxAHNyganhq-3oS9RwyX9Wa5l2enxgfvOS-AXUIwPaKymFRF0kwRcGpTlAWbkKlggsjihSD/s640/table-tcp-udp.png)

Most commonly used ports in web include: http, https, IRC, SNMP, IMAP, NTP, NNTP, POP3, DNS, SMTP, SSH, FTP, telnet.
