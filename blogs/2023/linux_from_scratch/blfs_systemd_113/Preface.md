
# Preface
--------

Having helped out with Linux From Scratch for a short time, I noticed that we were getting many queries as to how to do things beyond the base LFS system. At the time, the only assistance specifically offered relating to LFS were the LFS hints (https://www.linuxfromscratch.org/hints). Most of the LFS hints are extremely good and well written but I (and others) could still see a need for more comprehensive help to go Beyond LFS — hence BLFS.

BLFS aims to be more than the LFS-hints converted to XML although much of our work is based around the hints and indeed some authors write both hints and the relevant BLFS sections. We hope that we can provide you with enough information to not only manage to build your system up to what you want, whether it be a web server or a multimedia desktop system, but also that you will learn a lot about system configuration as you go.

Thanks as ever go to everyone in the LFS/BLFS community; especially those who have contributed instructions, written text, answered questions and generally shouted when things were wrong!

Finally, we encourage you to become involved in the community; ask questions on the mailing list or news gateway and join in the fun on #lfs and #lfs-support at Libera. You can find more details about all of these in the [Introduction](1.Welcome_to_blfs.md) section of the book.

Enjoy using BLFS.

    Mark Hymers
    markh <at> linuxfromscratch.org
    BLFS Editor (July 2001–March 2003)

I still remember how I found the BLFS project and started using the instructions that were completed at the time. I could not believe how wonderful it was to get an application up and running very quickly, with explanations as to why things were done a certain way. Unfortunately, for me, it wasn't long before I was opening applications that had nothing more than "To be done" on the page. I did what most would do, I waited for someone else to do it. It wasn't too long before I am looking through Bugzilla for something easy to do. As with any learning experience, the definition of what was easy kept changing.

We still encourage you to become involved as BLFS is never really finished. Contributing or just using, we hope you enjoy your BLFS experience.

    Larry Lawrence
    larry <at> linuxfromscratch.org
    BLFS Editor (March 2003–June 2004)

The BLFS project is a natural progression of LFS. Together, these projects provide a unique resource for the Open Source Community. They take the mystery out of the process of building a complete, functional software system from the source code contributed by many talented individuals throughout the world. They truly allow users to implement the slogan “Your distro, your rules”.

Our goal is to continue to provide the best resource available that shows you how to integrate many significant Open Source applications. Since these applications are constantly updated and new applications are developed, this book will never be complete. Additionally, there is always room for improvement in explaining the nuances of how to install the different packages. To make these improvements, we need your feedback. I encourage you to participate on the different mailing lists, news groups, and IRC channels to help meet these goals.

    Bruce Dubbs
    bdubbs <at> linuxfromscratch.org
    BLFS Editor (June 2004–December 2006 and February 2011–now)

My introduction to the [B]LFS project was actually by accident. I was trying to build a GNOME environment using some how-tos and other information I found on the web. A couple of times I ran into some build issues and Googling pulled up some old BLFS mailing list messages. Out for curiosity, I visited the Linux From Scratch web site and shortly thereafter was hooked. I've not used any other Linux distribution for personal use since.

I can't promise anyone will feel the sense of satisfaction I felt after building my first few systems using [B]LFS instructions, but I sincerely hope that your BLFS experience is as rewarding for you as it has been for me.

The BLFS project has grown significantly the last couple of years. There are more package instructions and related dependencies than ever before. The project requires your input for continued success. If you discover that you enjoy building BLFS, please consider helping out in any way you can. BLFS requires hundreds of hours of maintenance to keep it even semi-current. If you feel confident enough in your editing skills, please consider joining the BLFS team. Simply contributing to the mailing list discussions with sound advice and/or providing patches to the book's XML will probably result in you receiving an invitation to join the team.

    Randy McMurchy
    randy <at> linuxfromscratch.org
    BLFS Editor (December 2006–January 2011)


## Foreword

This version of the book is intended to be used when building on top of a system built using the LFS book. Every effort has been made to ensure accuracy and reliability of the instructions. Many people find that using the instructions in this book after building the current stable or development version of LFS provides a stable and very modern Linux system.

Enjoy!

    Randy McMurchy
    August 24th, 2008


## Who Would Want to Read this Book

This book is mainly aimed at those who have built a system based on the LFS book. It will also be useful for those who are using other distributions, and for one reason or another want to manually build software and need some assistance. Note that the material in this book, in particular the dependency listings, assumes that you are using a basic LFS system with every package listed in the LFS book already installed and configured. BLFS can be used to create a range of diverse systems and so the target audience is probably as wide as that of the LFS book. If you found LFS useful, you should also like this!

Since Release 7.4, the BLFS book version has matched the LFS book version. This book may be incompatible with a previous or later release of the LFS book.


## Organization

This book is divided into the following fourteen parts.

**Part I - Introduction**

    This part contains essential information which is needed to understand the rest of the book.

**Part II - Post LFS Configuration and Extra Software**

    Here we introduce basic configuration and security issues. We also discuss a range of text editors, file systems, and shells which aren't covered in the main LFS book.

**Part III - General Libraries and Utilities**

    In this section we cover libraries which are often needed throughout the book, as well as system utilities. Information on programming (including recompiling GCC to support its full range of languages) concludes this part.

**Part IV - Basic Networking**

    Here we explain how to connect to a network when you aren't using the simple static IP setup presented in the main LFS book. Networking libraries and command-line networking tools are also covered here.

**Part V - Servers**

    Here we show you how to set up mail and other servers (such as FTP, Apache, etc.).

**Part VI - X + Window Managers**

    This part explains how to set up a basic X Window System, along with some generic X libraries and Window managers.

**Part VII - KDE**

    This part is for those who want to use the K Desktop Environment, or parts of it.

**Part VIII - GNOME**

    GNOME is the main alternative to KDE in the Desktop Environment arena.

**Part IX - Xfce**

    Xfce is a lightweight alternative to GNOME and KDE.

**Part X - LXDE**

    LXDE is another lightweight alternative to GNOME and KDE.

**Part XI - More X Software**

    Office programs and graphical web browsers are important to most people. They, and some generic X software, can be found in this part of the book.

**Part XII - Multimedia**

    Here we cover multimedia libraries and drivers, along with some audio, video, and CD-writing programs.

**Part XIII - Printing, Scanning and Typesetting (PST)**

    This part covers document handling, from applications like Ghostscript, CUPS, and DocBook, all the way to texlive.

**Appendices**

    The Appendices present information which doesn't belong in the body of book; they are included as reference material. The glossary of acronyms is a handy feature.
