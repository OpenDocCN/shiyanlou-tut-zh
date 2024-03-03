# Return-to-libc 攻击实验

Return-to-libc 攻击是一种特殊的缓冲区溢出攻击，通常用于攻击有“栈不可执行”保护措施的目标系统。本实验中我们将用 system()地址替换返回地址，用它调用一个 root shell。