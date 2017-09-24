IT=chpst runit runit-init runsv runsvchdir runsvdir sv svlogd utmpset

LD = $(CC)

define choose
	result="$(4)"; \
	case "$(1)" in \
		*c*) $(CC) $(CFLAGS) -c $(2).c > /dev/null 2>&1 || result="$(3)";; \
	esac; \
	case "$(1)" in \
		*l*) $(LD) -s -o $(2) $(2).o > /dev/null 2>&1 || result="$(3)";; \
	esac; \
	case "$(1)" in \
		*r*) result="$(3)";; \
	esac; \
	ln -sf $$result $(5); \
	rm -rf $(2).o $(2); \
	echo $$result;
endef

default: sysdeps $(IT)

check: $(IT)
	./check-local $(IT)

runit: load runit.o unix.a byte.a
	$(LD) -s -o runit runit.o unix.a byte.a -static

runit-init: load runit-init.o unix.a byte.a
	$(LD) -s -o runit-init runit-init.o unix.a byte.a -static

runsv: load runsv.o unix.a byte.a time.a
	$(LD) -s -o runsv runsv.o unix.a byte.a time.a

runsvdir: load runsvdir.o unix.a byte.a time.a
	$(LD) -s -o runsvdir runsvdir.o unix.a byte.a time.a

runsvstat: load runsvstat.o unix.a byte.a time.a
	$(LD) -s -o runsvstat runsvstat.o unix.a byte.a time.a

runsvctrl: load runsvctrl.o unix.a byte.a
	$(LD) -s -o runsvctrl runsvctrl.o unix.a byte.a

sv: load sv.o unix.a byte.a time.a
	$(LD) -s -o sv sv.o unix.a byte.a time.a

svwaitup: load svwaitup.o unix.a byte.a time.a
	$(LD) -s -o svwaitup svwaitup.o unix.a byte.a time.a

svwaitdown: load svwaitdown.o unix.a byte.a time.a
	$(LD) -s -o svwaitdown svwaitdown.o unix.a byte.a time.a

utmpset: load utmpset.o unix.a byte.a
	$(LD) -s -o utmpset utmpset.o unix.a byte.a

runsvchdir: load runsvchdir.o unix.a byte.a
	$(LD) -s -o runsvchdir runsvchdir.o unix.a byte.a

svlogd: load svlogd.o pmatch.o fmt_ptime.o unix.a byte.a time.a socket.lib
	$(LD) -s -o svlogd svlogd.o pmatch.o fmt_ptime.o unix.a byte.a time.a \
	`cat socket.lib`

chpst: load chpst.o uidgid.o unix.a byte.a
	$(LD) -s -o chpst chpst.o uidgid.o unix.a byte.a

runit.o: compile sysdeps runit.c
	$(CC) $(CFLAGS) -c runit.c

runit-init.o: compile runit-init.c
	$(CC) $(CFLAGS) -c runit-init.c

runsv.o: compile sysdeps runsv.c
	$(CC) $(CFLAGS) -c runsv.c

runsvdir.o: compile sysdeps runsvdir.c
	$(CC) $(CFLAGS) -c runsvdir.c

runsvstat.o: compile sysdeps runsvstat.c
	$(CC) $(CFLAGS) -c runsvstat.c

runsvctrl.o: compile runsvctrl.c
	$(CC) $(CFLAGS) -c runsvctrl.c

sv.o: compile sysdeps sv.c
	$(CC) $(CFLAGS) -c sv.c

svwaitup.o: compile sysdeps svwaitup.c
	$(CC) $(CFLAGS) -c svwaitup.c

svwaitdown.o: compile sysdeps svwaitdown.c
	$(CC) $(CFLAGS) -c svwaitdown.c

utmpset.o: compile sysdeps utmpset.c
	$(CC) $(CFLAGS) -c utmpset.c

runsvchdir.o: compile runsvchdir.c
	$(CC) $(CFLAGS) -c runsvchdir.c

svlogd.o: compile sysdeps svlogd.c
	$(CC) $(CFLAGS) -c svlogd.c

chpst.o: compile sysdeps chpst.c
	$(CC) $(CFLAGS) -c chpst.c


uidgid.o: compile uidgid.c uidgid.h
	$(CC) $(CFLAGS) -c uidgid.c

pmatch.o: compile pmatch.c
	$(CC) $(CFLAGS) -c pmatch.c

fmt_ptime.o: compile sysdeps fmt_ptime.c
	$(CC) $(CFLAGS) -c fmt_ptime.c

reboot_system.h: choose compile reboot_system.h1 reboot_system.h2
	$(call choose,c,tryreboot,reboot_system.h1,reboot_system.h2,reboot_system.h)

uw_tmp.h: compile uw_tmp.h1 uw_tmp.h2
	( $(CC) $(CFLAGS) -c tryuwtmpx.c 2>/dev/null && cat uw_tmp.h2 >uw_tmp.h ) || \
	( $(CC) $(CFLAGS) -c tryuwtmp.c 2>/dev/null && cat uw_tmp.h1 >uw_tmp.h )
	rm -f tryuwtmp.o tryuwtmpx.o

socket.lib: compile load trysocketlib.c
	$(CC) $(CFLAGS) -c trysocketlib.c
	( $(LD) -s -o trysocketlib trysocketlib.o >/dev/null 2>&1 || \
	  ( $(LD) -s -o trysocketlib trysocketlib.o -lxnet >/dev/null 2>&1 && echo '-lxnet' ) || \
	  ( $(LD) -s -o trysocketlib trysocketlib.o -lsocket -lnsl >/dev/null 2>&1 && \
	    echo '-lsocket -lnsl' ) \
	) >socket.lib
	rm -f trysocketlib.o trysocketlib

clean:
	find . -name \*~ -exec rm -f {} \;
	find . -name .??*~ -exec rm -f {} \;
	find . -name \#?* -exec rm -f {} \;
	rm -f `cat TARGETS`

alloc.o: alloc.c alloc.h compile error.h
	$(CC) $(CFLAGS) -c alloc.c

alloc_re.o: alloc.h alloc_re.c byte.h compile
	$(CC) $(CFLAGS) -c alloc_re.c

buffer.o: buffer.c buffer.h compile
	$(CC) $(CFLAGS) -c buffer.c

buffer_0.o: buffer.h buffer_0.c compile
	$(CC) $(CFLAGS) -c buffer_0.c

buffer_1.o: buffer.h buffer_1.c compile
	$(CC) $(CFLAGS) -c buffer_1.c

buffer_2.o: buffer.h buffer_2.c compile
	$(CC) $(CFLAGS) -c buffer_2.c

buffer_get.o: buffer.h buffer_get.c byte.h compile error.h
	$(CC) $(CFLAGS) -c buffer_get.c

buffer_put.o: buffer.h buffer_put.c byte.h compile error.h str.h
	$(CC) $(CFLAGS) -c buffer_put.c

buffer_read.o: buffer.h buffer_read.c compile
	$(CC) $(CFLAGS) -c buffer_read.c

buffer_write.o: buffer.h buffer_write.c compile
	$(CC) $(CFLAGS) -c buffer_write.c

byte.a: byte_chr.o byte_copy.o byte_cr.o byte_diff.o byte_rchr.o \
fmt_uint.o fmt_uint0.o fmt_ulong.o makelib scan_ulong.o str_chr.o \
str_diff.o str_len.o str_start.o
	$(AR) cr byte.a byte_chr.o byte_copy.o byte_cr.o byte_diff.o \
	byte_rchr.o fmt_uint.o fmt_uint0.o fmt_ulong.o scan_ulong.o str_chr.o \
	str_diff.o str_len.o str_start.o

byte_chr.o: byte.h byte_chr.c compile
	$(CC) $(CFLAGS) -c byte_chr.c

byte_copy.o: byte.h byte_copy.c compile
	$(CC) $(CFLAGS) -c byte_copy.c

byte_cr.o: byte.h byte_cr.c compile
	$(CC) $(CFLAGS) -c byte_cr.c

byte_diff.o: byte.h byte_diff.c compile
	$(CC) $(CFLAGS) -c byte_diff.c

byte_rchr.o: byte.h byte_rchr.c compile
	$(CC) $(CFLAGS) -c byte_rchr.c

chkshsgr: chkshsgr.o load
	$(LD) -s -o chkshsgr chkshsgr.o

host-chkshsgr: host-chkshsgr.o
	gcc -s -o host-chkshsgr host-chkshsgr.o 

chkshsgr.o: chkshsgr.c compile
	$(CC) $(CFLAGS) -c chkshsgr.c

host-chkshsgr.o: chkshsgr.c
	ln -sf chkshsgr.c host-chkshsgr.c
	gcc $(CFLAGS) -c host-chkshsgr.c -o host-chkshsgr.o

choose: choose.sh warn-auto.sh
	rm -f choose
	cat warn-auto.sh choose.sh \
	> choose
	chmod 555 choose

coe.o: coe.c coe.h compile
	$(CC) $(CFLAGS) -c coe.c

compile: conf-cc print-cc.sh systype warn-auto.sh
	rm -f compile
	sh print-cc.sh > compile
	chmod 555 compile

direntry.h: choose compile direntry.h1 direntry.h2 trydrent.c
	$(call choose,c,trydrent,direntry.h1,direntry.h2,direntry.h)

env.o: compile env.c env.h str.h
	$(CC) $(CFLAGS) -c env.c

error.o: compile error.c error.h
	$(CC) $(CFLAGS) -c error.c

error_str.o: compile error.h error_str.c
	$(CC) $(CFLAGS) -c error_str.c

fd_copy.o: compile fd.h fd_copy.c
	$(CC) $(CFLAGS) -c fd_copy.c

fd_move.o: compile fd.h fd_move.c
	$(CC) $(CFLAGS) -c fd_move.c

fifo.o: compile fifo.c fifo.h hasmkffo.h
	$(CC) $(CFLAGS) -c fifo.c

fmt_uint.o: compile fmt.h fmt_uint.c
	$(CC) $(CFLAGS) -c fmt_uint.c

fmt_uint0.o: compile fmt.h fmt_uint0.c
	$(CC) $(CFLAGS) -c fmt_uint0.c

fmt_ulong.o: compile fmt.h fmt_ulong.c
	$(CC) $(CFLAGS) -c fmt_ulong.c

hasflock.h: choose compile hasflock.h1 hasflock.h2 load tryflock.c
	$(call choose,cl,tryflock,hasflock.h1,hasflock.h2,hasflock.h)

hasmkffo.h: choose compile hasmkffo.h1 hasmkffo.h2 load trymkffo.c
	$(call choose,cl,trymkffo,hasmkffo.h1,hasmkffo.h2,hasmkffo.h)

hassgact.h: choose compile hassgact.h1 hassgact.h2 load trysgact.c
	$(call choose,cl,trysgact,hassgact.h1,hassgact.h2,hassgact.h)

hassgprm.h: choose compile hassgprm.h1 hassgprm.h2 load trysgprm.c
	$(call choose,cl,trysgprm,hassgprm.h1,hassgprm.h2,hassgprm.h)

hasshsgr.h: host-chkshsgr choose compile hasshsgr.h1 hasshsgr.h2 load \
tryshsgr.c warn-shsgr
	./host-chkshsgr || ( cat warn-shsgr; exit 1 )
	$(call choose,clr,tryshsgr,hasshsgr.h1,hasshsgr.h2,hasshsgr.h)

haswaitp.h: choose compile haswaitp.h1 haswaitp.h2 load trywaitp.c
	$(call choose,cl,trywaitp,haswaitp.h1,haswaitp.h2,haswaitp.h)

iopause.h: choose compile iopause.h1 iopause.h2 load trypoll.c
	$(call choose,clr,trypoll,iopause.h1,iopause.h2,iopause.h)

iopause.o: compile iopause.c iopause.h select.h tai.h taia.h uint64.h
	$(CC) $(CFLAGS) -c iopause.c

load: conf-ld print-ld.sh systype warn-auto.sh
	rm -f load
	sh print-ld.sh > load
	chmod 555 load

lock_ex.o: compile hasflock.h lock.h lock_ex.c
	$(CC) $(CFLAGS) -c lock_ex.c

lock_exnb.o: compile hasflock.h lock.h lock_exnb.c
	$(CC) $(CFLAGS) -c lock_exnb.c

makelib: print-ar.sh systype warn-auto.sh
	rm -f makelib
	sh print-ar.sh > makelib
	chmod 555 makelib

ndelay_off.o: compile ndelay.h ndelay_off.c
	$(CC) $(CFLAGS) -c ndelay_off.c

ndelay_on.o: compile ndelay.h ndelay_on.c
	$(CC) $(CFLAGS) -c ndelay_on.c

open_append.o: compile open.h open_append.c
	$(CC) $(CFLAGS) -c open_append.c

open_read.o: compile open.h open_read.c
	$(CC) $(CFLAGS) -c open_read.c

open_trunc.o: compile open.h open_trunc.c
	$(CC) $(CFLAGS) -c open_trunc.c

open_write.o: compile open.h open_write.c
	$(CC) $(CFLAGS) -c open_write.c

openreadclose.o: compile error.h gen_alloc.h open.h openreadclose.c \
openreadclose.h readclose.h stralloc.h
	$(CC) $(CFLAGS) -c openreadclose.c

pathexec_env.o: alloc.h byte.h compile env.h gen_alloc.h pathexec.h \
pathexec_env.c str.h stralloc.h
	$(CC) $(CFLAGS) -c pathexec_env.c

pathexec_run.o: compile env.h error.h gen_alloc.h pathexec.h \
pathexec_run.c str.h stralloc.h
	$(CC) $(CFLAGS) -c pathexec_run.c

prot.o: compile hasshsgr.h prot.c prot.h
	$(CC) $(CFLAGS) -c prot.c

readclose.o: compile error.h gen_alloc.h readclose.c readclose.h \
stralloc.h
	$(CC) $(CFLAGS) -c readclose.c

scan_ulong.o: compile scan.h scan_ulong.c
	$(CC) $(CFLAGS) -c scan_ulong.c

seek_set.o: compile seek.h seek_set.c
	$(CC) $(CFLAGS) -c seek_set.c

select.h: choose compile select.h1 select.h2 trysysel.c
	$(call choose,c,trysysel,select.h1,select.h2,select.h)

sgetopt.o: buffer.h compile sgetopt.c sgetopt.h subgetopt.h
	$(CC) $(CFLAGS) -c sgetopt.c

sig.o: compile sig.c sig.h
	$(CC) $(CFLAGS) -c sig.c

sig_block.o: compile hassgprm.h sig.h sig_block.c
	$(CC) $(CFLAGS) -c sig_block.c

sig_catch.o: compile hassgact.h sig.h sig_catch.c
	$(CC) $(CFLAGS) -c sig_catch.c

sig_pause.o: compile hassgprm.h sig.h sig_pause.c
	$(CC) $(CFLAGS) -c sig_pause.c

str_chr.o: compile str.h str_chr.c
	$(CC) $(CFLAGS) -c str_chr.c

str_diff.o: compile str.h str_diff.c
	$(CC) $(CFLAGS) -c str_diff.c

str_len.o: compile str.h str_len.c
	$(CC) $(CFLAGS) -c str_len.c

str_start.o: compile str.h str_start.c
	$(CC) $(CFLAGS) -c str_start.c

stralloc_cat.o: byte.h compile gen_alloc.h stralloc.h stralloc_cat.c
	$(CC) $(CFLAGS) -c stralloc_cat.c

stralloc_catb.o: byte.h compile gen_alloc.h stralloc.h \
stralloc_catb.c
	$(CC) $(CFLAGS) -c stralloc_catb.c

stralloc_cats.o: byte.h compile gen_alloc.h str.h stralloc.h \
stralloc_cats.c
	$(CC) $(CFLAGS) -c stralloc_cats.c

stralloc_eady.o: alloc.h compile gen_alloc.h gen_allocdefs.h \
stralloc.h stralloc_eady.c
	$(CC) $(CFLAGS) -c stralloc_eady.c

stralloc_opyb.o: byte.h compile gen_alloc.h stralloc.h \
stralloc_opyb.c
	$(CC) $(CFLAGS) -c stralloc_opyb.c

stralloc_opys.o: byte.h compile gen_alloc.h str.h stralloc.h \
stralloc_opys.c
	$(CC) $(CFLAGS) -c stralloc_opys.c

stralloc_pend.o: alloc.h compile gen_alloc.h gen_allocdefs.h \
stralloc.h stralloc_pend.c
	$(CC) $(CFLAGS) -c stralloc_pend.c

strerr_die.o: buffer.h compile strerr.h strerr_die.c
	$(CC) $(CFLAGS) -c strerr_die.c

strerr_sys.o: compile error.h strerr.h strerr_sys.c
	$(CC) $(CFLAGS) -c strerr_sys.c

subgetopt.o: compile subgetopt.c subgetopt.h
	$(CC) $(CFLAGS) -c subgetopt.c

sysdeps: compile direntry.h hasflock.h hasmkffo.h hassgact.h \
hassgprm.h hasshsgr.h haswaitp.h iopause.h load select.h systype \
uint64.h reboot_system.h uw_tmp.h socket.lib
	rm -f sysdeps
	cat systype compile load socket.lib >>sysdeps
	grep sysdep direntry.h >>sysdeps
	grep sysdep haswaitp.h >>sysdeps
	grep sysdep hassgact.h >>sysdeps
	grep sysdep hassgprm.h >>sysdeps
	grep sysdep select.h >>sysdeps
	grep sysdep uint64.h >>sysdeps
	grep sysdep iopause.h >>sysdeps
	grep sysdep hasmkffo.h >>sysdeps
	grep sysdep hasflock.h >>sysdeps
	grep sysdep hasshsgr.h >>sysdeps
	grep sysdep reboot_system.h >>sysdeps
	grep sysdep uw_tmp.h >>sysdeps
	cat sysdeps

systype: find-systype.sh trycpp.c x86cpuid.c
	sh find-systype.sh > systype

tai_now.o: compile tai.h tai_now.c uint64.h
	$(CC) $(CFLAGS) -c tai_now.c

tai_pack.o: compile tai.h tai_pack.c uint64.h
	$(CC) $(CFLAGS) -c tai_pack.c

tai_sub.o: compile tai.h tai_sub.c uint64.h
	$(CC) $(CFLAGS) -c tai_sub.c

tai_unpack.o: compile tai.h tai_unpack.c uint64.h
	$(CC) $(CFLAGS) -c tai_unpack.c

taia_add.o: compile tai.h taia.h taia_add.c uint64.h
	$(CC) $(CFLAGS) -c taia_add.c

taia_approx.o: compile tai.h taia.h taia_approx.c uint64.h
	$(CC) $(CFLAGS) -c taia_approx.c

taia_frac.o: compile tai.h taia.h taia_frac.c uint64.h
	$(CC) $(CFLAGS) -c taia_frac.c

taia_less.o: compile tai.h taia.h taia_less.c uint64.h
	$(CC) $(CFLAGS) -c taia_less.c

taia_now.o: compile tai.h taia.h taia_now.c uint64.h
	$(CC) $(CFLAGS) -c taia_now.c

taia_pack.o: compile tai.h taia.h taia_pack.c uint64.h
	$(CC) $(CFLAGS) -c taia_pack.c

taia_sub.o: compile tai.h taia.h taia_sub.c uint64.h
	$(CC) $(CFLAGS) -c taia_sub.c

taia_uint.o: compile tai.h taia.h taia_uint.c uint64.h
	$(CC) $(CFLAGS) -c taia_uint.c

time.a: iopause.o makelib tai_now.o tai_pack.o tai_sub.o tai_unpack.o \
taia_add.o taia_approx.o taia_frac.o taia_less.o taia_now.o \
taia_pack.o taia_sub.o taia_uint.o
	$(AR) cr time.a iopause.o tai_now.o tai_pack.o tai_sub.o \
	tai_unpack.o taia_add.o taia_approx.o taia_frac.o taia_less.o \
	taia_now.o taia_pack.o taia_sub.o taia_uint.o

uint64.h: choose compile load tryulong64.c uint64.h1 uint64.h2
	$(call choose,clr,tryulogn64,uint64.h1,uint64.h2,uint64.h)

unix.a: alloc.o alloc_re.o buffer.o buffer_0.o buffer_1.o buffer_2.o \
buffer_get.o buffer_put.o buffer_read.o buffer_write.o coe.o env.o \
error.o error_str.o fd_copy.o fd_move.o fifo.o lock_ex.o lock_exnb.o \
makelib ndelay_off.o ndelay_on.o open_append.o open_read.o \
open_trunc.o open_write.o openreadclose.o pathexec_env.o \
pathexec_run.o prot.o readclose.o seek_set.o sgetopt.o sig.o \
sig_block.o sig_catch.o sig_pause.o stralloc_cat.o stralloc_catb.o \
stralloc_cats.o stralloc_eady.o stralloc_opyb.o stralloc_opys.o \
stralloc_pend.o strerr_die.o strerr_sys.o subgetopt.o wait_nohang.o \
wait_pid.o
	$(AR) cr unix.a alloc.o alloc_re.o buffer.o buffer_0.o buffer_1.o \
	buffer_2.o buffer_get.o buffer_put.o buffer_read.o buffer_write.o \
	coe.o env.o error.o error_str.o fd_copy.o fd_move.o fifo.o lock_ex.o \
	lock_exnb.o ndelay_off.o ndelay_on.o open_append.o open_read.o \
	open_trunc.o open_write.o openreadclose.o pathexec_env.o \
	pathexec_run.o prot.o readclose.o seek_set.o sgetopt.o sig.o \
	sig_block.o sig_catch.o sig_pause.o stralloc_cat.o stralloc_catb.o \
	stralloc_cats.o stralloc_eady.o stralloc_opyb.o stralloc_opys.o \
	stralloc_pend.o strerr_die.o strerr_sys.o subgetopt.o wait_nohang.o \
	wait_pid.o

wait_nohang.o: compile haswaitp.h wait_nohang.c
	$(CC) $(CFLAGS) -c wait_nohang.c

wait_pid.o: compile error.h haswaitp.h wait_pid.c
	$(CC) $(CFLAGS) -c wait_pid.c

