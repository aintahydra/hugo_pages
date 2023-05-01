## install peda

1. Clone PEDA
```
git clone https://github.com/longld/peda.git ~/peda
```
2. set gdbinit
```
$ vi ~/.gdbinit
define init-peda
source ~/peda/peda.py
end

document init-peda
Init PEDA
end
```
3. Activate peda
```
$ gdb
(gdb) init-peda
gdb-peda$
```
