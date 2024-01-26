
# The Linux WatchDog Timer Power Management Guide

Last reviewed: 17-Dec-2018

Wolfram Sang <wsa+renesas@sang-engineering.com>


# 1. Introduction

本文档规定了有关看门狗设备及其电源管理处理的规则，以确保 Linux 系统的统一行为。


# 2. Ping on resume

恢复时，看门狗定时器应重置为其选定的值，以便为用户空间提供足够的时间来恢复。 [1] [2]

[1] <https://patchwork.kernel.org/patch/10252209/>

[2] <https://patchwork.kernel.org/patch/10711625/>
