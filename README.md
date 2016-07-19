# Axis2C 安装过程
## 1设置环境变量 ##
	export AXIS2C_HOME=/usr/local/axis2c

## 2.下载源码包解压编译安装 ##

	cd axis2c-src-1.6.0
	./configure --prefix=$AXIS2C_HOME --enable-tests=no--enable-amqp=no 
	--enable-libxml2=yes
注意要加 --enable-libxml2=yes 不然会提示

	Making all in test
	make[4]: Entering directory `/home/ec2-user/axis2c-src-1.7.0/neethi/test'
	make[4]: *** No rule to make target `../../axiom/src/parser/guththila/libaxis2_parser.la', needed by `test'. Stop.

	make && sudo -E make install
## 3.安装程序样例 

	cd samples
	CFLAGS=-I$AXIS2C_HOME/include/axis2-1.6.0 LDFLAGS=-L$AXIS2C_HOME/lib ./configure --prefix=$AXIS2C_HOME
	make && sudo -E make install

## 4.启动服务 ##
	cd $AXIS2C_HOME/bin
	./axis2_http_server
启用调试模式

	cd $AXIS2C_HOME/bin
	STAFF_LOG_LEVEL=DEBUG3 STAFF_EXCEPTION_STACKTRACING=1./axis2_http_server
## 5.测试是否安装成功 ##
打开链接
[http://localhost:9090/axis2/services](http://localhost:9090/axis2/services)

## 6.启动axis2c中例子 ##

	root@linux-desktop:/usr/local/axis2c/samples/bin# ls
	amqp                echo_blocking_dual      google         resources
	echo                echo_blocking_soap11    math           version
	echo_blocking       echo_non_blocking       mtom           yahoosearch
	echo_blocking_addr  echo_non_blocking_dual  mtom_callback
	echo_blocking_auth  echo_rest               notify
	root@linux-desktop:/usr/local/axis2c/samples/bin# ./math 
	Using endpoint : http://localhost:9090/axis2/services/math
	
	Invoking operation add with params 40 and 8
	
	Result = 48
	root@linux-desktop:/usr/local/axis2c/samples/bin# 

# 安装出错处理 #
## 1.提示添加到系统动态链接库 ##
	Libraries have been installed in:
	   /usr/local/axis2c/samples/lib/mtom_sending_callback
	
	If you ever happen to want to link against installed libraries
	in a given directory, LIBDIR, you must either use libtool, and
	specify the full pathname of the library, or use the `-LLIBDIR'
	flag during linking and do at least one of the following:
	   - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
	     during execution
	   - add LIBDIR to the `LD_RUN_PATH' environment variable
	     during linking
	   - use the `-Wl,--rpath -Wl,LIBDIR' linker flag
	   - have your system administrator add LIBDIR to `/etc/ld.so.conf'
	
	See any operating system documentation about shared libraries for
	more information, such as the ld(1) and ld.so(8) manual pages.

以上提示信息是在安装axis2c例子程序出现，提示将axis2c的链接库文件添加到系统`/etc/ld.so.conf`中方便以后程序编译链接，可忽略。
## 2.axis2c编译出错提示ndefined reference to symbol 'axiom_xml_reader_free' ##
官方补丁解决办法

	--- neethi/test/Makefile.am.orig
	+++ neethi/test/Makefile.am
	@@ -13,4 +13,5 @@ INCLUDES = -I$(top_builddir)/include \
	 test_LDADD = $(top_builddir)/src/libneethi.la \
	 			../../axiom/src/om/libaxis2_axiom.la \
	 			../../util/src/libaxutil.la \
	+			../../axiom/src/parser/libxml2/libaxis2_parser.la \
	 			../src/libneethi.la
	--- neethi/test/Makefile.in.orig
	+++ neethi/test/Makefile.in
	@@ -49,7 +49,8 @@ am_test_OBJECTS = test.$(OBJEXT)
	 test_OBJECTS = $(am_test_OBJECTS)
	 test_DEPENDENCIES = $(top_builddir)/src/libneethi.la \
	 	../../axiom/src/om/libaxis2_axiom.la \
	-	../../util/src/libaxutil.la ../src/libneethi.la
	+	../../util/src/libaxutil.la \
	+	../../axiom/src/parser/libxml2/libaxis2_parser.la ../src/libneethi.la
	 DEFAULT_INCLUDES = -I. -I$(top_builddir)@am__isrc@
	 depcomp = $(SHELL) $(top_srcdir)/depcomp
	 am__depfiles_maybe = depfiles
	@@ -188,6 +189,7 @@ INCLUDES = -I$(top_builddir)/include \
	 test_LDADD = $(top_builddir)/src/libneethi.la \
	 			../../axiom/src/om/libaxis2_axiom.la \
	 			../../util/src/libaxutil.la \
	+			../../axiom/src/parser/libxml2/libaxis2_parser.la \
	 			../src/libneethi.la
	 
	 all: all-am
修改neethi/test/中的 Makefile.am 和Makefile.in文件中内容
也可以直接用修改后文件直接替换
[Makefile.am](./Makefile.am)

[Makefile.in](./Makefile.in)

[官方补丁axis2c_neethiTest_linkdep](axis2c_neethiTest_linkdep.patch)