# 审计事件表¶

下表包含了在整个 CPython 运行时和标准库中由 [`sys.audit()`](sys.md#sys.audit "sys.audit") 或 [`PySys_Audit()`](../c-api/sys.md#c.PySys_Audit "PySys_Audit") 调用所引发的全部事件。 这些调用是在 3.8.0 或更高版本中添加了 (参见 [**PEP 578**](https://peps.python.org/pep-0578/))。

请参阅 [`sys.addaudithook()`](sys.md#sys.addaudithook "sys.addaudithook") 和 [`PySys_AddAuditHook()`](../c-api/sys.md#c.PySys_AddAuditHook "PySys_AddAuditHook") 了解有关处理这些事件的详细信息。

**CPython 实现细节：** 此表是根据 CPython 文档生成的，可能无法表示其他实现所引发的事件。 请参阅你的运行时专属的文档了解实际引发的事件。

Audit event

|

Arguments

|

References  
  
---|---|---  
  
_thread.start_new_thread

|

`function`, `args`, `kwargs`

|

[[1]](_thread.md#start_new_thread)  
  
array.__new__

|

`typecode`, `initializer`

|

[[1]](array.md#array.array)  
  
builtins.breakpoint

|

`breakpointhook`

|

[[1]](functions.md#breakpoint)  
  
builtins.id

|

`id`

|

[[1]](functions.md#id)  
  
builtins.input

|

`prompt`

|

[[1]](functions.md#input)  
  
builtins.input/result

|

`result`

|

[[1]](functions.md#input)  
  
code.__new__

|

`code`, `filename`, `name`, `argcount`, `posonlyargcount`, `kwonlyargcount`, `nlocals`, `stacksize`, `flags`

|

[[1]](types.md#types.CodeType)  
  
compile

|

`source`, `filename`

|

[[1]](functions.md#compile)  
  
cpython.PyInterpreterState_Clear

|

|

[[1]](../c-api/init.md#c.PyInterpreterState_Clear)  
  
cpython.PyInterpreterState_New

|

|

[[1]](../c-api/init.md#c.PyInterpreterState_New)  
  
cpython._PySys_ClearAuditHooks

|

|

[[1]](../c-api/init.md#c.Py_FinalizeEx)  
  
cpython.run_command

|

`command`

|

[[1]](../using/cmdline.md#cmdoption-c)  
  
cpython.run_file

|

`filename`

|

[[1]](../using/cmdline.md#audit_event_cpython_run_file_0)  
  
cpython.run_interactivehook

|

`hook`

|

[[1]](sys.md#sys.__interactivehook__)  
  
cpython.run_module

|

`module-name`

|

[[1]](../using/cmdline.md#cmdoption-m)  
  
cpython.run_startup

|

`filename`

|

[[1]](../using/cmdline.md#envvar-PYTHONSTARTUP)  
  
cpython.run_stdin

|

|

[[1]](../using/cmdline.md#audit_event_cpython_run_stdin_0)  
  
ctypes.addressof

|

`obj`

|

[[1]](ctypes.md#ctypes.addressof)  
  
ctypes.call_function

|

`func_pointer`, `arguments`

|

[[1]](ctypes.md#foreign-functions)  
  
ctypes.cdata

|

`address`

|

[[1]](ctypes.md#ctypes._CData.from_address)  
  
ctypes.cdata/buffer

|

`pointer`, `size`, `offset`

|

[[1]](ctypes.md#ctypes._CData.from_buffer)[[2]](ctypes.md#ctypes._CData.from_buffer_copy)  
  
ctypes.create_string_buffer

|

`init`, `size`

|

[[1]](ctypes.md#ctypes.create_string_buffer)  
  
ctypes.create_unicode_buffer

|

`init`, `size`

|

[[1]](ctypes.md#ctypes.create_unicode_buffer)  
  
ctypes.dlopen

|

`name`

|

[[1]](ctypes.md#ctypes.LibraryLoader)  
  
ctypes.dlsym

|

`library`, `name`

|

[[1]](ctypes.md#ctypes.LibraryLoader)  
  
ctypes.dlsym/handle

|

`handle`, `name`

|

[[1]](ctypes.md#ctypes.LibraryLoader)  
  
ctypes.get_errno

|

|

[[1]](ctypes.md#ctypes.get_errno)  
  
ctypes.get_last_error

|

|

[[1]](ctypes.md#ctypes.get_last_error)  
  
ctypes.set_errno

|

`errno`

|

[[1]](ctypes.md#ctypes.set_errno)  
  
ctypes.set_exception

|

`code`

|

[[1]](ctypes.md#foreign-functions)  
  
ctypes.set_last_error

|

`error`

|

[[1]](ctypes.md#ctypes.set_last_error)  
  
ctypes.string_at

|

`address`, `size`

|

[[1]](ctypes.md#ctypes.string_at)  
  
ctypes.wstring_at

|

`address`, `size`

|

[[1]](ctypes.md#ctypes.wstring_at)  
  
ensurepip.bootstrap

|

`root`

|

[[1]](ensurepip.md#ensurepip.bootstrap)  
  
exec

|

`code_object`

|

[[1]](functions.md#eval)[[2]](functions.md#exec)  
  
fcntl.fcntl

|

`fd`, `cmd`, `arg`

|

[[1]](fcntl.md#fcntl.fcntl)  
  
fcntl.flock

|

`fd`, `operation`

|

[[1]](fcntl.md#fcntl.flock)  
  
fcntl.ioctl

|

`fd`, `request`, `arg`

|

[[1]](fcntl.md#fcntl.ioctl)  
  
fcntl.lockf

|

`fd`, `cmd`, `len`, `start`, `whence`

|

[[1]](fcntl.md#fcntl.lockf)  
  
ftplib.connect

|

`self`, `host`, `port`

|

[[1]](ftplib.md#ftplib.FTP.connect)  
  
ftplib.sendcmd

|

`self`, `cmd`

|

[[1]](ftplib.md#ftplib.FTP.sendcmd)[[2]](ftplib.md#ftplib.FTP.voidcmd)  
  
function.__new__

|

`code`

|

[[1]](types.md#types.FunctionType)  
  
gc.get_objects

|

`generation`

|

[[1]](gc.md#gc.get_objects)  
  
gc.get_referents

|

`objs`

|

[[1]](gc.md#gc.get_referents)  
  
gc.get_referrers

|

`objs`

|

[[1]](gc.md#gc.get_referrers)  
  
glob.glob

|

`pathname`, `recursive`

|

[[1]](glob.md#glob.glob)[[2]](glob.md#glob.iglob)  
  
glob.glob/2

|

`pathname`, `recursive`, `root_dir`, `dir_fd`

|

[[1]](glob.md#glob.glob)[[2]](glob.md#glob.iglob)  
  
http.client.connect

|

`self`, `host`, `port`

|

[[1]](http.client.md#http.client.HTTPConnection.connect)  
  
http.client.send

|

`self`, `data`

|

[[1]](http.client.md#http.client.HTTPConnection.send)  
  
imaplib.open

|

`self`, `host`, `port`

|

[[1]](imaplib.md#imaplib.IMAP4.open)  
  
imaplib.send

|

`self`, `data`

|

[[1]](imaplib.md#imaplib.IMAP4.send)  
  
import

|

`module`, `filename`, `sys.path`, `sys.meta_path`, `sys.path_hooks`

|

[[1]](../reference/simple_stmts.md#import)  
  
marshal.dumps

|

`value`, `version`

|

[[1]](marshal.md#marshal.dump)  
  
marshal.load

|

|

[[1]](marshal.md#marshal.load)  
  
marshal.loads

|

`bytes`

|

[[1]](marshal.md#marshal.load)  
  
mmap.__new__

|

`fileno`, `length`, `access`, `offset`

|

[[1]](mmap.md#mmap.mmap)  
  
msvcrt.get_osfhandle

|

`fd`

|

[[1]](msvcrt.md#msvcrt.get_osfhandle)  
  
msvcrt.locking

|

`fd`, `mode`, `nbytes`

|

[[1]](msvcrt.md#msvcrt.locking)  
  
msvcrt.open_osfhandle

|

`handle`, `flags`

|

[[1]](msvcrt.md#msvcrt.open_osfhandle)  
  
object.__delattr__

|

`obj`, `name`

|

[[1]](../reference/datamodel.md#object.__delattr__)  
  
object.__getattr__

|

`obj`, `name`

|

[[1]](../reference/datamodel.md#object.__getattribute__)  
  
object.__setattr__

|

`obj`, `name`, `value`

|

[[1]](../reference/datamodel.md#object.__setattr__)  
  
open

|

`path`, `mode`, `flags`

|

[[1]](functions.md#open)[[2]](io.md#io.open)[[3]](os.md#os.open)  
  
os.add_dll_directory

|

`path`

|

[[1]](os.md#os.add_dll_directory)  
  
os.chdir

|

`path`

|

[[1]](os.md#os.chdir)[[2]](os.md#os.fchdir)  
  
os.chflags

|

`path`, `flags`

|

[[1]](os.md#os.chflags)[[2]](os.md#os.lchflags)  
  
os.chmod

|

`path`, `mode`, `dir_fd`

|

[[1]](os.md#os.chmod)[[2]](os.md#os.fchmod)[[3]](os.md#os.lchmod)  
  
os.chown

|

`path`, `uid`, `gid`, `dir_fd`

|

[[1]](os.md#os.chown)[[2]](os.md#os.fchown)[[3]](os.md#os.lchown)  
  
os.exec

|

`path`, `args`, `env`

|

[[1]](os.md#os.execl)  
  
os.fork

|

|

[[1]](os.md#os.fork)  
  
os.forkpty

|

|

[[1]](os.md#os.forkpty)  
  
os.fwalk

|

`top`, `topdown`, `onerror`, `follow_symlinks`, `dir_fd`

|

[[1]](os.md#os.fwalk)  
  
os.getxattr

|

`path`, `attribute`

|

[[1]](os.md#os.getxattr)  
  
os.kill

|

`pid`, `sig`

|

[[1]](os.md#os.kill)  
  
os.killpg

|

`pgid`, `sig`

|

[[1]](os.md#os.killpg)  
  
os.link

|

`src`, `dst`, `src_dir_fd`, `dst_dir_fd`

|

[[1]](os.md#os.link)  
  
os.listdir

|

`path`

|

[[1]](os.md#os.listdir)  
  
os.listdrives

|

|

[[1]](os.md#os.listdrives)  
  
os.listmounts

|

`volume`

|

[[1]](os.md#os.listmounts)  
  
os.listvolumes

|

|

[[1]](os.md#os.listvolumes)  
  
os.listxattr

|

`path`

|

[[1]](os.md#os.listxattr)  
  
os.lockf

|

`fd`, `cmd`, `len`

|

[[1]](os.md#os.lockf)  
  
os.mkdir

|

`path`, `mode`, `dir_fd`

|

[[1]](os.md#os.makedirs)[[2]](os.md#os.mkdir)  
  
os.posix_spawn

|

`path`, `argv`, `env`

|

[[1]](os.md#os.posix_spawn)[[2]](os.md#os.posix_spawnp)  
  
os.putenv

|

`key`, `value`

|

[[1]](os.md#os.putenv)  
  
os.remove

|

`path`, `dir_fd`

|

[[1]](os.md#os.remove)[[2]](os.md#os.removedirs)[[3]](os.md#os.unlink)  
  
os.removexattr

|

`path`, `attribute`

|

[[1]](os.md#os.removexattr)  
  
os.rename

|

`src`, `dst`, `src_dir_fd`, `dst_dir_fd`

|

[[1]](os.md#os.rename)[[2]](os.md#os.renames)[[3]](os.md#os.replace)  
  
os.rmdir

|

`path`, `dir_fd`

|

[[1]](os.md#os.rmdir)  
  
os.scandir

|

`path`

|

[[1]](os.md#os.scandir)  
  
os.setxattr

|

`path`, `attribute`, `value`, `flags`

|

[[1]](os.md#os.setxattr)  
  
os.spawn

|

`mode`, `path`, `args`, `env`

|

[[1]](os.md#os.spawnl)  
  
os.startfile

|

`path`, `operation`

|

[[1]](os.md#os.startfile)  
  
os.startfile/2

|

`path`, `operation`, `arguments`, `cwd`, `show_cmd`

|

[[1]](os.md#os.startfile)  
  
os.symlink

|

`src`, `dst`, `dir_fd`

|

[[1]](os.md#os.symlink)  
  
os.system

|

`command`

|

[[1]](os.md#os.system)  
  
os.truncate

|

`fd`, `length`

|

[[1]](os.md#os.ftruncate)[[2]](os.md#os.truncate)  
  
os.unsetenv

|

`key`

|

[[1]](os.md#os.unsetenv)  
  
os.utime

|

`path`, `times`, `ns`, `dir_fd`

|

[[1]](os.md#os.utime)  
  
os.walk

|

`top`, `topdown`, `onerror`, `followlinks`

|

[[1]](os.md#os.walk)  
  
pathlib.Path.glob

|

`self`, `pattern`

|

[[1]](pathlib.md#pathlib.Path.glob)  
  
pathlib.Path.rglob

|

`self`, `pattern`

|

[[1]](pathlib.md#pathlib.Path.rglob)  
  
pdb.Pdb

|

|

[[1]](pdb.md#pdb.Pdb)  
  
pickle.find_class

|

`module`, `name`

|

[[1]](pickle.md#pickle.Unpickler.find_class)  
  
poplib.connect

|

`self`, `host`, `port`

|

[[1]](poplib.md#poplib.POP3)[[2]](poplib.md#poplib.POP3_SSL)  
  
poplib.putline

|

`self`, `line`

|

[[1]](poplib.md#poplib.POP3)[[2]](poplib.md#poplib.POP3_SSL)  
  
pty.spawn

|

`argv`

|

[[1]](pty.md#pty.spawn)  
  
resource.prlimit

|

`pid`, `resource`, `limits`

|

[[1]](resource.md#resource.prlimit)  
  
resource.setrlimit

|

`resource`, `limits`

|

[[1]](resource.md#resource.setrlimit)  
  
setopencodehook

|

|

[[1]](../c-api/file.md#c.PyFile_SetOpenCodeHook)  
  
shutil.chown

|

`path`, `user`, `group`

|

[[1]](shutil.md#shutil.chown)  
  
shutil.copyfile

|

`src`, `dst`

|

[[1]](shutil.md#shutil.copy)[[2]](shutil.md#shutil.copy2)[[3]](shutil.md#shutil.copyfile)  
  
shutil.copymode

|

`src`, `dst`

|

[[1]](shutil.md#shutil.copy)[[2]](shutil.md#shutil.copymode)  
  
shutil.copystat

|

`src`, `dst`

|

[[1]](shutil.md#shutil.copy2)[[2]](shutil.md#shutil.copystat)  
  
shutil.copytree

|

`src`, `dst`

|

[[1]](shutil.md#shutil.copytree)  
  
shutil.make_archive

|

`base_name`, `format`, `root_dir`, `base_dir`

|

[[1]](shutil.md#shutil.make_archive)  
  
shutil.move

|

`src`, `dst`

|

[[1]](shutil.md#shutil.move)  
  
shutil.rmtree

|

`path`, `dir_fd`

|

[[1]](shutil.md#shutil.rmtree)  
  
shutil.unpack_archive

|

`filename`, `extract_dir`, `format`

|

[[1]](shutil.md#shutil.unpack_archive)  
  
signal.pthread_kill

|

`thread_id`, `signalnum`

|

[[1]](signal.md#signal.pthread_kill)  
  
smtplib.connect

|

`self`, `host`, `port`

|

[[1]](smtplib.md#smtplib.SMTP.connect)  
  
smtplib.send

|

`self`, `data`

|

[[1]](smtplib.md#smtplib.SMTP)  
  
socket.__new__

|

`self`, `family`, `type`, `protocol`

|

[[1]](socket.md#socket.socket)  
  
socket.bind

|

`self`, `address`

|

[[1]](socket.md#socket.socket.bind)  
  
socket.connect

|

`self`, `address`

|

[[1]](socket.md#socket.socket.connect)[[2]](socket.md#socket.socket.connect_ex)  
  
socket.getaddrinfo

|

`host`, `port`, `family`, `type`, `protocol`

|

[[1]](socket.md#socket.getaddrinfo)  
  
socket.gethostbyaddr

|

`ip_address`

|

[[1]](socket.md#socket.gethostbyaddr)  
  
socket.gethostbyname

|

`hostname`

|

[[1]](socket.md#socket.gethostbyname)[[2]](socket.md#socket.gethostbyname_ex)  
  
socket.gethostname

|

|

[[1]](socket.md#socket.gethostname)  
  
socket.getnameinfo

|

`sockaddr`

|

[[1]](socket.md#socket.getnameinfo)  
  
socket.getservbyname

|

`servicename`, `protocolname`

|

[[1]](socket.md#socket.getservbyname)  
  
socket.getservbyport

|

`port`, `protocolname`

|

[[1]](socket.md#socket.getservbyport)  
  
socket.sendmsg

|

`self`, `address`

|

[[1]](socket.md#socket.socket.sendmsg)  
  
socket.sendto

|

`self`, `address`

|

[[1]](socket.md#socket.socket.sendto)  
  
socket.sethostname

|

`name`

|

[[1]](socket.md#socket.sethostname)  
  
sqlite3.connect

|

`database`

|

[[1]](sqlite3.md#sqlite3.connect)  
  
sqlite3.connect/handle

|

`connection_handle`

|

[[1]](sqlite3.md#sqlite3.connect)  
  
sqlite3.enable_load_extension

|

`connection`, `enabled`

|

[[1]](sqlite3.md#sqlite3.Connection.enable_load_extension)  
  
sqlite3.load_extension

|

`connection`, `path`

|

[[1]](sqlite3.md#sqlite3.Connection.load_extension)  
  
subprocess.Popen

|

`executable`, `args`, `cwd`, `env`

|

[[1]](subprocess.md#subprocess.Popen)  
  
sys._current_exceptions

|

|

[[1]](sys.md#sys._current_exceptions)  
  
sys._current_frames

|

|

[[1]](sys.md#sys._current_frames)  
  
sys._getframe

|

`frame`

|

[[1]](sys.md#sys._getframe)  
  
sys._getframemodulename

|

`depth`

|

[[1]](sys.md#sys._getframemodulename)  
  
sys.addaudithook

|

|

[[1]](../c-api/sys.md#c.PySys_AddAuditHook)[[2]](sys.md#sys.addaudithook)  
  
sys.excepthook

|

`hook`, `type`, `value`, `traceback`

|

[[1]](sys.md#sys.excepthook)  
  
sys.set_asyncgen_hooks_finalizer

|

|

[[1]](sys.md#sys.set_asyncgen_hooks)  
  
sys.set_asyncgen_hooks_firstiter

|

|

[[1]](sys.md#sys.set_asyncgen_hooks)  
  
sys.setprofile

|

|

[[1]](sys.md#sys.setprofile)  
  
sys.settrace

|

|

[[1]](sys.md#sys.settrace)  
  
sys.unraisablehook

|

`hook`, `unraisable`

|

[[1]](sys.md#sys.unraisablehook)  
  
syslog.closelog

|

|

[[1]](syslog.md#syslog.closelog)  
  
syslog.openlog

|

`ident`, `logoption`, `facility`

|

[[1]](syslog.md#syslog.openlog)  
  
syslog.setlogmask

|

`maskpri`

|

[[1]](syslog.md#syslog.setlogmask)  
  
syslog.syslog

|

`priority`, `message`

|

[[1]](syslog.md#syslog.syslog)  
  
tempfile.mkdtemp

|

`fullpath`

|

[[1]](tempfile.md#tempfile.TemporaryDirectory)[[2]](tempfile.md#tempfile.mkdtemp)  
  
tempfile.mkstemp

|

`fullpath`

|

[[1]](tempfile.md#tempfile.NamedTemporaryFile)[[2]](tempfile.md#tempfile.TemporaryFile)[[3]](tempfile.md#tempfile.mkstemp)  
  
time.sleep

|

`secs`

|

[[1]](time.md#audit_event_time_sleep_0)  
  
urllib.Request

|

`fullurl`, `data`, `headers`, `method`

|

[[1]](urllib.request.md#urllib.request.urlopen)  
  
webbrowser.open

|

`url`

|

[[1]](webbrowser.md#webbrowser.open)  
  
winreg.ConnectRegistry

|

`computer_name`, `key`

|

[[1]](winreg.md#winreg.ConnectRegistry)  
  
winreg.CreateKey

|

`key`, `sub_key`, `access`

|

[[1]](winreg.md#winreg.CreateKey)[[2]](winreg.md#winreg.CreateKeyEx)  
  
winreg.DeleteKey

|

`key`, `sub_key`, `access`

|

[[1]](winreg.md#winreg.DeleteKey)[[2]](winreg.md#winreg.DeleteKeyEx)  
  
winreg.DeleteValue

|

`key`, `value`

|

[[1]](winreg.md#winreg.DeleteValue)  
  
winreg.DisableReflectionKey

|

`key`

|

[[1]](winreg.md#winreg.DisableReflectionKey)  
  
winreg.EnableReflectionKey

|

`key`

|

[[1]](winreg.md#winreg.EnableReflectionKey)  
  
winreg.EnumKey

|

`key`, `index`

|

[[1]](winreg.md#winreg.EnumKey)  
  
winreg.EnumValue

|

`key`, `index`

|

[[1]](winreg.md#winreg.EnumValue)  
  
winreg.ExpandEnvironmentStrings

|

`str`

|

[[1]](winreg.md#winreg.ExpandEnvironmentStrings)  
  
winreg.LoadKey

|

`key`, `sub_key`, `file_name`

|

[[1]](winreg.md#winreg.LoadKey)  
  
winreg.OpenKey

|

`key`, `sub_key`, `access`

|

[[1]](winreg.md#winreg.OpenKey)  
  
winreg.OpenKey/result

|

`key`

|

[[1]](winreg.md#winreg.CreateKey)[[2]](winreg.md#winreg.CreateKeyEx)[[3]](winreg.md#winreg.OpenKey)  
  
winreg.PyHKEY.Detach

|

`key`

|

[[1]](winreg.md#winreg.PyHKEY.Detach)  
  
winreg.QueryInfoKey

|

`key`

|

[[1]](winreg.md#winreg.QueryInfoKey)  
  
winreg.QueryReflectionKey

|

`key`

|

[[1]](winreg.md#winreg.QueryReflectionKey)  
  
winreg.QueryValue

|

`key`, `sub_key`, `value_name`

|

[[1]](winreg.md#winreg.QueryValue)[[2]](winreg.md#winreg.QueryValueEx)  
  
winreg.SaveKey

|

`key`, `file_name`

|

[[1]](winreg.md#winreg.SaveKey)  
  
winreg.SetValue

|

`key`, `sub_key`, `type`, `value`

|

[[1]](winreg.md#winreg.SetValue)[[2]](winreg.md#winreg.SetValueEx)  
  
下列事件只在内部被引发，而不会回应任何 CPython 公共 API:

审计事件

|

实参  
  
---|---  
  
_winapi.CreateFile

|

`file_name`, `desired_access`, `share_mode`, `creation_disposition`, `flags_and_attributes`  
  
_winapi.CreateJunction

|

`src_path`, `dst_path`  
  
_winapi.CreateNamedPipe

|

`name`, `open_mode`, `pipe_mode`  
  
_winapi.CreatePipe

|  
  
_winapi.CreateProcess

|

`application_name`, `command_line`, `current_directory`  
  
_winapi.OpenProcess

|

`process_id`, `desired_access`  
  
_winapi.TerminateProcess

|

`handle`, `exit_code`  
  
ctypes.PyObj_FromPtr

|

`obj`  
  
