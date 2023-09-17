# 数据持久化¶

本章中描述的模块支持在磁盘上以持久形式存储 Python 数据。 [`pickle`](pickle.md#module-pickle "pickle: Convert Python objects to streams of bytes and back.") 和 [`marshal`](marshal.md#module-marshal "marshal: Convert Python objects to streams of bytes and back \(with different constraints\).") 模块可以将许多 Python 数据类型转换为字节流，然后从字节中重新创建对象。 各种与 DBM 相关的模块支持一系列基于散列的文件格式，这些格式存储字符串到其他字符串的映射。

本章中描述的模块列表是：

  * [`pickle` \--- Python 对象序列化](pickle.md)
    * [与其他 Python 模块间的关系](pickle.md#relationship-to-other-python-modules)
      * [与 `marshal` 间的关系](pickle.md#comparison-with-marshal)
      * [与 `json` 模块的比较](pickle.md#comparison-with-json)
    * [数据流格式](pickle.md#data-stream-format)
    * [模块接口](pickle.md#module-interface)
      * [`HIGHEST_PROTOCOL`](pickle.md#pickle.HIGHEST_PROTOCOL)
      * [`DEFAULT_PROTOCOL`](pickle.md#pickle.DEFAULT_PROTOCOL)
      * [`dump()`](pickle.md#pickle.dump)
      * [`dumps()`](pickle.md#pickle.dumps)
      * [`load()`](pickle.md#pickle.load)
      * [`loads()`](pickle.md#pickle.loads)
      * [`PickleError`](pickle.md#pickle.PickleError)
      * [`PicklingError`](pickle.md#pickle.PicklingError)
      * [`UnpicklingError`](pickle.md#pickle.UnpicklingError)
      * [`Pickler`](pickle.md#pickle.Pickler)
        * [`Pickler.dump()`](pickle.md#pickle.Pickler.dump)
        * [`Pickler.persistent_id()`](pickle.md#pickle.Pickler.persistent_id)
        * [`Pickler.dispatch_table`](pickle.md#pickle.Pickler.dispatch_table)
        * [`Pickler.reducer_override()`](pickle.md#pickle.Pickler.reducer_override)
        * [`Pickler.fast`](pickle.md#pickle.Pickler.fast)
      * [`Unpickler`](pickle.md#pickle.Unpickler)
        * [`Unpickler.load()`](pickle.md#pickle.Unpickler.load)
        * [`Unpickler.persistent_load()`](pickle.md#pickle.Unpickler.persistent_load)
        * [`Unpickler.find_class()`](pickle.md#pickle.Unpickler.find_class)
      * [`PickleBuffer`](pickle.md#pickle.PickleBuffer)
        * [`PickleBuffer.raw()`](pickle.md#pickle.PickleBuffer.raw)
        * [`PickleBuffer.release()`](pickle.md#pickle.PickleBuffer.release)
    * [可以被封存/解封的对象](pickle.md#what-can-be-pickled-and-unpickled)
    * [封存类实例](pickle.md#pickling-class-instances)
      * [`object.__getnewargs_ex__()`](pickle.md#object.__getnewargs_ex__)
      * [`object.__getnewargs__()`](pickle.md#object.__getnewargs__)
      * [`object.__getstate__()`](pickle.md#object.__getstate__)
      * [`object.__setstate__()`](pickle.md#object.__setstate__)
      * [`object.__reduce__()`](pickle.md#object.__reduce__)
      * [`object.__reduce_ex__()`](pickle.md#object.__reduce_ex__)
      * [持久化外部对象](pickle.md#persistence-of-external-objects)
      * [Dispatch 表](pickle.md#dispatch-tables)
      * [处理有状态的对象](pickle.md#handling-stateful-objects)
    * [类型，函数和其他对象的自定义归约](pickle.md#custom-reduction-for-types-functions-and-other-objects)
    * [外部缓冲区](pickle.md#out-of-band-buffers)
      * [提供方 API](pickle.md#provider-api)
      * [使用方 API](pickle.md#consumer-api)
      * [示例](pickle.md#example)
    * [限制全局变量](pickle.md#restricting-globals)
    * [性能](pickle.md#performance)
    * [例子](pickle.md#examples)
  * [`copyreg` \--- 注册配合 `pickle` 模块使用的函数](copyreg.md)
    * [`constructor()`](copyreg.md#copyreg.constructor)
    * [`pickle()`](copyreg.md#copyreg.pickle)
    * [示例](copyreg.md#example)
  * [`shelve` \--- Python 对象持久化](shelve.md)
    * [`open()`](shelve.md#shelve.open)
    * [`Shelf.sync()`](shelve.md#shelve.Shelf.sync)
    * [`Shelf.close()`](shelve.md#shelve.Shelf.close)
    * [限制](shelve.md#restrictions)
      * [`Shelf`](shelve.md#shelve.Shelf)
      * [`BsdDbShelf`](shelve.md#shelve.BsdDbShelf)
      * [`DbfilenameShelf`](shelve.md#shelve.DbfilenameShelf)
    * [示例](shelve.md#example)
  * [`marshal` \--- 内部 Python 对象序列化](marshal.md)
    * [`dump()`](marshal.md#marshal.dump)
    * [`load()`](marshal.md#marshal.load)
    * [`dumps()`](marshal.md#marshal.dumps)
    * [`loads()`](marshal.md#marshal.loads)
    * [`version`](marshal.md#marshal.version)
  * [`dbm` \--- Unix "数据库" 接口](dbm.md)
    * [`error`](dbm.md#dbm.error)
    * [`whichdb()`](dbm.md#dbm.whichdb)
    * [`open()`](dbm.md#dbm.open)
    * [`dbm.gnu` \--- GNU 对 dbm 的重解析](dbm.md#module-dbm.gnu)
      * [`error`](dbm.md#dbm.gnu.error)
      * [`open()`](dbm.md#dbm.gnu.open)
      * [`gdbm.firstkey()`](dbm.md#dbm.gnu.gdbm.firstkey)
      * [`gdbm.nextkey()`](dbm.md#dbm.gnu.gdbm.nextkey)
      * [`gdbm.reorganize()`](dbm.md#dbm.gnu.gdbm.reorganize)
      * [`gdbm.sync()`](dbm.md#dbm.gnu.gdbm.sync)
      * [`gdbm.close()`](dbm.md#dbm.gnu.gdbm.close)
      * [`gdbm.clear()`](dbm.md#dbm.gnu.gdbm.clear)
    * [`dbm.ndbm` \--- 基于 ndbm 的接口](dbm.md#module-dbm.ndbm)
      * [`error`](dbm.md#dbm.ndbm.error)
      * [`library`](dbm.md#dbm.ndbm.library)
      * [`open()`](dbm.md#dbm.ndbm.open)
      * [`ndbm.close()`](dbm.md#dbm.ndbm.ndbm.close)
      * [`ndbm.clear()`](dbm.md#dbm.ndbm.ndbm.clear)
    * [`dbm.dumb` \--- 便携式 DBM 实现](dbm.md#module-dbm.dumb)
      * [`error`](dbm.md#dbm.dumb.error)
      * [`open()`](dbm.md#dbm.dumb.open)
      * [`dumbdbm.sync()`](dbm.md#dbm.dumb.dumbdbm.sync)
      * [`dumbdbm.close()`](dbm.md#dbm.dumb.dumbdbm.close)
  * [`sqlite3` \--- SQLite 数据库 DB-API 2.0 接口模块](sqlite3.md)
    * [教程](sqlite3.md#tutorial)
    * [参考](sqlite3.md#reference)
      * [模块函数](sqlite3.md#module-functions)
        * [`connect()`](sqlite3.md#sqlite3.connect)
        * [`complete_statement()`](sqlite3.md#sqlite3.complete_statement)
        * [`enable_callback_tracebacks()`](sqlite3.md#sqlite3.enable_callback_tracebacks)
        * [`register_adapter()`](sqlite3.md#sqlite3.register_adapter)
        * [`register_converter()`](sqlite3.md#sqlite3.register_converter)
      * [模块常量](sqlite3.md#module-constants)
        * [`LEGACY_TRANSACTION_CONTROL`](sqlite3.md#sqlite3.LEGACY_TRANSACTION_CONTROL)
        * [`PARSE_COLNAMES`](sqlite3.md#sqlite3.PARSE_COLNAMES)
        * [`PARSE_DECLTYPES`](sqlite3.md#sqlite3.PARSE_DECLTYPES)
        * [`SQLITE_OK`](sqlite3.md#sqlite3.SQLITE_OK)
        * [`SQLITE_DENY`](sqlite3.md#sqlite3.SQLITE_DENY)
        * [`SQLITE_IGNORE`](sqlite3.md#sqlite3.SQLITE_IGNORE)
        * [`apilevel`](sqlite3.md#sqlite3.apilevel)
        * [`paramstyle`](sqlite3.md#sqlite3.paramstyle)
        * [`sqlite_version`](sqlite3.md#sqlite3.sqlite_version)
        * [`sqlite_version_info`](sqlite3.md#sqlite3.sqlite_version_info)
        * [`threadsafety`](sqlite3.md#sqlite3.threadsafety)
        * [`version`](sqlite3.md#sqlite3.version)
        * [`version_info`](sqlite3.md#sqlite3.version_info)
        * [`SQLITE_DBCONFIG_DEFENSIVE`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_DEFENSIVE)
        * [`SQLITE_DBCONFIG_DQS_DDL`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_DQS_DDL)
        * [`SQLITE_DBCONFIG_DQS_DML`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_DQS_DML)
        * [`SQLITE_DBCONFIG_ENABLE_FKEY`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_ENABLE_FKEY)
        * [`SQLITE_DBCONFIG_ENABLE_FTS3_TOKENIZER`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_ENABLE_FTS3_TOKENIZER)
        * [`SQLITE_DBCONFIG_ENABLE_LOAD_EXTENSION`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_ENABLE_LOAD_EXTENSION)
        * [`SQLITE_DBCONFIG_ENABLE_QPSG`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_ENABLE_QPSG)
        * [`SQLITE_DBCONFIG_ENABLE_TRIGGER`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_ENABLE_TRIGGER)
        * [`SQLITE_DBCONFIG_ENABLE_VIEW`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_ENABLE_VIEW)
        * [`SQLITE_DBCONFIG_LEGACY_ALTER_TABLE`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_LEGACY_ALTER_TABLE)
        * [`SQLITE_DBCONFIG_LEGACY_FILE_FORMAT`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_LEGACY_FILE_FORMAT)
        * [`SQLITE_DBCONFIG_NO_CKPT_ON_CLOSE`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_NO_CKPT_ON_CLOSE)
        * [`SQLITE_DBCONFIG_RESET_DATABASE`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_RESET_DATABASE)
        * [`SQLITE_DBCONFIG_TRIGGER_EQP`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_TRIGGER_EQP)
        * [`SQLITE_DBCONFIG_TRUSTED_SCHEMA`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_TRUSTED_SCHEMA)
        * [`SQLITE_DBCONFIG_WRITABLE_SCHEMA`](sqlite3.md#sqlite3.SQLITE_DBCONFIG_WRITABLE_SCHEMA)
      * [连接对象](sqlite3.md#connection-objects)
        * [`Connection`](sqlite3.md#sqlite3.Connection)
          * [`Connection.cursor()`](sqlite3.md#sqlite3.Connection.cursor)
          * [`Connection.blobopen()`](sqlite3.md#sqlite3.Connection.blobopen)
          * [`Connection.commit()`](sqlite3.md#sqlite3.Connection.commit)
          * [`Connection.rollback()`](sqlite3.md#sqlite3.Connection.rollback)
          * [`Connection.close()`](sqlite3.md#sqlite3.Connection.close)
          * [`Connection.execute()`](sqlite3.md#sqlite3.Connection.execute)
          * [`Connection.executemany()`](sqlite3.md#sqlite3.Connection.executemany)
          * [`Connection.executescript()`](sqlite3.md#sqlite3.Connection.executescript)
          * [`Connection.create_function()`](sqlite3.md#sqlite3.Connection.create_function)
          * [`Connection.create_aggregate()`](sqlite3.md#sqlite3.Connection.create_aggregate)
          * [`Connection.create_window_function()`](sqlite3.md#sqlite3.Connection.create_window_function)
          * [`Connection.create_collation()`](sqlite3.md#sqlite3.Connection.create_collation)
          * [`Connection.interrupt()`](sqlite3.md#sqlite3.Connection.interrupt)
          * [`Connection.set_authorizer()`](sqlite3.md#sqlite3.Connection.set_authorizer)
          * [`Connection.set_progress_handler()`](sqlite3.md#sqlite3.Connection.set_progress_handler)
          * [`Connection.set_trace_callback()`](sqlite3.md#sqlite3.Connection.set_trace_callback)
          * [`Connection.enable_load_extension()`](sqlite3.md#sqlite3.Connection.enable_load_extension)
          * [`Connection.load_extension()`](sqlite3.md#sqlite3.Connection.load_extension)
          * [`Connection.iterdump()`](sqlite3.md#sqlite3.Connection.iterdump)
          * [`Connection.backup()`](sqlite3.md#sqlite3.Connection.backup)
          * [`Connection.getlimit()`](sqlite3.md#sqlite3.Connection.getlimit)
          * [`Connection.setlimit()`](sqlite3.md#sqlite3.Connection.setlimit)
          * [`Connection.getconfig()`](sqlite3.md#sqlite3.Connection.getconfig)
          * [`Connection.setconfig()`](sqlite3.md#sqlite3.Connection.setconfig)
          * [`Connection.serialize()`](sqlite3.md#sqlite3.Connection.serialize)
          * [`Connection.deserialize()`](sqlite3.md#sqlite3.Connection.deserialize)
          * [`Connection.autocommit`](sqlite3.md#sqlite3.Connection.autocommit)
          * [`Connection.in_transaction`](sqlite3.md#sqlite3.Connection.in_transaction)
          * [`Connection.isolation_level`](sqlite3.md#sqlite3.Connection.isolation_level)
          * [`Connection.row_factory`](sqlite3.md#sqlite3.Connection.row_factory)
          * [`Connection.text_factory`](sqlite3.md#sqlite3.Connection.text_factory)
          * [`Connection.total_changes`](sqlite3.md#sqlite3.Connection.total_changes)
      * [游标对象](sqlite3.md#cursor-objects)
        * [`Cursor`](sqlite3.md#sqlite3.Cursor)
          * [`Cursor.execute()`](sqlite3.md#sqlite3.Cursor.execute)
          * [`Cursor.executemany()`](sqlite3.md#sqlite3.Cursor.executemany)
          * [`Cursor.executescript()`](sqlite3.md#sqlite3.Cursor.executescript)
          * [`Cursor.fetchone()`](sqlite3.md#sqlite3.Cursor.fetchone)
          * [`Cursor.fetchmany()`](sqlite3.md#sqlite3.Cursor.fetchmany)
          * [`Cursor.fetchall()`](sqlite3.md#sqlite3.Cursor.fetchall)
          * [`Cursor.close()`](sqlite3.md#sqlite3.Cursor.close)
          * [`Cursor.setinputsizes()`](sqlite3.md#sqlite3.Cursor.setinputsizes)
          * [`Cursor.setoutputsize()`](sqlite3.md#sqlite3.Cursor.setoutputsize)
          * [`Cursor.arraysize`](sqlite3.md#sqlite3.Cursor.arraysize)
          * [`Cursor.connection`](sqlite3.md#sqlite3.Cursor.connection)
          * [`Cursor.description`](sqlite3.md#sqlite3.Cursor.description)
          * [`Cursor.lastrowid`](sqlite3.md#sqlite3.Cursor.lastrowid)
          * [`Cursor.rowcount`](sqlite3.md#sqlite3.Cursor.rowcount)
          * [`Cursor.row_factory`](sqlite3.md#sqlite3.Cursor.row_factory)
      * [Row 对象](sqlite3.md#row-objects)
        * [`Row`](sqlite3.md#sqlite3.Row)
          * [`Row.keys()`](sqlite3.md#sqlite3.Row.keys)
      * [Blob 对象](sqlite3.md#blob-objects)
        * [`Blob`](sqlite3.md#sqlite3.Blob)
          * [`Blob.close()`](sqlite3.md#sqlite3.Blob.close)
          * [`Blob.read()`](sqlite3.md#sqlite3.Blob.read)
          * [`Blob.write()`](sqlite3.md#sqlite3.Blob.write)
          * [`Blob.tell()`](sqlite3.md#sqlite3.Blob.tell)
          * [`Blob.seek()`](sqlite3.md#sqlite3.Blob.seek)
      * [PrepareProtocol 对象](sqlite3.md#prepareprotocol-objects)
        * [`PrepareProtocol`](sqlite3.md#sqlite3.PrepareProtocol)
      * [异常](sqlite3.md#exceptions)
        * [`Warning`](sqlite3.md#sqlite3.Warning)
        * [`Error`](sqlite3.md#sqlite3.Error)
          * [`Error.sqlite_errorcode`](sqlite3.md#sqlite3.Error.sqlite_errorcode)
          * [`Error.sqlite_errorname`](sqlite3.md#sqlite3.Error.sqlite_errorname)
        * [`InterfaceError`](sqlite3.md#sqlite3.InterfaceError)
        * [`DatabaseError`](sqlite3.md#sqlite3.DatabaseError)
        * [`DataError`](sqlite3.md#sqlite3.DataError)
        * [`OperationalError`](sqlite3.md#sqlite3.OperationalError)
        * [`IntegrityError`](sqlite3.md#sqlite3.IntegrityError)
        * [`InternalError`](sqlite3.md#sqlite3.InternalError)
        * [`ProgrammingError`](sqlite3.md#sqlite3.ProgrammingError)
        * [`NotSupportedError`](sqlite3.md#sqlite3.NotSupportedError)
      * [SQLite 与 Python 类型](sqlite3.md#sqlite-and-python-types)
      * [默认适配器和转换器（已弃用）](sqlite3.md#default-adapters-and-converters-deprecated)
      * [命令行接口](sqlite3.md#command-line-interface)
    * [常用方案指引](sqlite3.md#how-to-guides)
      * [如何在 SQL 查询中使用占位符来绑定值](sqlite3.md#how-to-use-placeholders-to-bind-values-in-sql-queries)
      * [如何将自定义 Python 类型适配到 SQLite 值](sqlite3.md#how-to-adapt-custom-python-types-to-sqlite-values)
        * [如何编写可适配对象](sqlite3.md#how-to-write-adaptable-objects)
        * [如何注册适配器可调用对象](sqlite3.md#how-to-register-adapter-callables)
      * [如何将 SQLite 值转换为自定义 Python 类型](sqlite3.md#how-to-convert-sqlite-values-to-custom-python-types)
      * [适配器和转换器范例程序](sqlite3.md#adapter-and-converter-recipes)
      * [如何使用连接快捷方法](sqlite3.md#how-to-use-connection-shortcut-methods)
      * [如何使用连接上下文管理器](sqlite3.md#how-to-use-the-connection-context-manager)
      * [如何使用 SQLite URI](sqlite3.md#how-to-work-with-sqlite-uris)
      * [如何创建并使用行工厂对象](sqlite3.md#how-to-create-and-use-row-factories)
    * [说明](sqlite3.md#explanation)
      * [事务控制](sqlite3.md#transaction-control)
        * [通过 `autocommit` 属性进行事务控制](sqlite3.md#transaction-control-via-the-autocommit-attribute)
        * [通过 `isolation_level` 属性进行事务控制](sqlite3.md#transaction-control-via-the-isolation-level-attribute)
