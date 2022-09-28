Description: <short summary of the patch>
 TODO: Put a short summary on the line above and replace this paragraph
 with a longer explanation of this change. Complete the meta-information
 with other relevant fields (see below for details). To make it easier, the
 information below has been extracted from the changelog. Adjust it or drop
 it.
 .
 bogl (0.1.18-12) UNRELEASED; urgency=medium
 .
   * patches: Switch to 3.0 patch system.
   * control: Bump Standards-Version to 3.9.8 (no change).
   * Use canonical anonscm vcs URL.
   * patches/gcc-7: Fix build with gcc-7 (Closes: Bug#853335)
   * patches/cub: Support cub blindly emitted by busybox.
Author: Samuel Thibault <sthibault@debian.org>
Bug-Debian: https://bugs.debian.org/853335

---
The information above should follow the Patch Tagging Guidelines, please
checkout http://dep.debian.net/deps/dep3/ to learn about the format. Here
are templates for supplementary fields that you might want to add:

Origin: <vendor|upstream|other>, <url of original patch>
Bug: <url in upstream bugtracker>
Bug-Debian: https://bugs.debian.org/<bugnumber>
Bug-Ubuntu: https://launchpad.net/bugs/<bugnumber>
Forwarded: <no|not-needed|url proving that it has been forwarded>
Reviewed-By: <name and email of someone who approved the patch>
Last-Update: 2017-02-22

--- bogl-0.1.18.orig/Makefile
+++ bogl-0.1.18/Makefile
@@ -8,7 +8,8 @@ CFLAGS = -O2 -g -D_GNU_SOURCE
 WARNCFLAGS += -Wall -D_GNU_SOURCE
 ALLCFLAGS = $(CFLAGS) $(WARNCFLAGS) $(FBCFLAGS)
 
-architecture := $(shell dpkg-architecture -qDEB_BUILD_GNU_CPU)
+architecture := $(shell dpkg-architecture -qDEB_HOST_ARCH_CPU)
+os := $(shell dpkg-architecture -qDEB_HOST_ARCH_OS)
 
 LIBOBJECTS = $(LIBBOGLOBJECTS) $(LIBBOMLOBJECTS) $(LIBBOWLOBJECTS)	\
 	$(LIBRSRCOBJECTS)
@@ -18,27 +19,35 @@ LIBBOWLOBJECTS = bowl.o symbol.o
 LIBRSRCOBJECTS = helvB10.o helvB12.o helvR10.o timBI18.o tux75.o
 
 SOURCES_DEP = arrow.c bdftobogl.c bogl-cfb.c bogl-cfb.h bogl-cfb8.c	\
-bogl-cfb8.h bogl-font.c bogl-font.h bogl-pcfb.c bogl-pcfb.h		\
-bogl-tcfb.c bogl-tcfb.h bogl-test.c bogl.c bogl.h boglP.h boml.c	\
-boml.h bowl-boxes.c bowl.c bowl.h pngtobogl.c
+bogl-cfb8.h bogl-font.c bogl-font.h \
+bogl-test.c bogl.h boglP.h boml.c	\
+boml.h bowl.c bowl.h pngtobogl.c
+
+ifeq ($(os),linux)
+	SOURCES_DEP += bogl-pcfb.c bogl-pcfb.h bogl-tcfb.c bogl-tcfb.h bowl-boxes.c bogl.c
 
 ifeq (,)
 	FBCFLAGS += -DBOGL_CFB_FB=1
 	LIBBOGLOBJECTS += bogl-cfb.o bogl-pcfb.o bogl-tcfb.o
 endif
 
-ifneq (,$(filter i386 arm ia64 x86_64,$(architecture)))
+ifneq (,$(filter i386 ia64 amd64,$(architecture)))
 	FBCFLAGS += -DBOGL_VGA16_FB=1
 	LIBBOGLOBJECTS += bogl-vga16.o
 	SOURCES_DEP += bogl-vga16.c bogl-vga16.h
 endif
+endif
 
 OBJECTS = $(LIBOBJECTS) bowl-boxes.o
 
 GENERATED = helvB10.c helvB12.c helvR10.c timBI18.c tux75.c
 
 #		 libutf8/libutf8_plug.so unifont-reduced.bgf
-all:    depend $(SHARED_LIB) $(LIB) bterm bdftobogl reduce-font
+all:    depend
+ifeq ($(os),linux)
+all: $(SHARED_LIB) $(LIB) bterm bdftobogl pngtobogl
+endif
+all: reduce-font
 
 %.lo: %.c
 	$(CC) $(ALLCFLAGS) -o $@ -fPIC -c $<
@@ -86,7 +95,7 @@ bdftobogl: $(LIBBOGLOBJECTS)
 	./bdftobogl $< > $@
 
 pngtobogl: pngtobogl.o
-	$(CC) $(ALLCFLAGS) -o pngtobogl pngtobogl.o -lgd -lpng -lm
+	$(CC) $(ALLCFLAGS) -o pngtobogl pngtobogl.o -lgd
 %.c: %.png pngtobogl
 	./pngtobogl $< > $@
 
@@ -109,12 +118,16 @@ include .depend
 endif
 
 install: all
-	install -d $(DESTDIR)/usr/lib $(DESTDIR)/usr/include/bogl $(DESTDIR)/usr/bin
+	install -d $(DESTDIR)/usr/bin
+ifeq ($(os),linux)
+	install -d $(DESTDIR)/usr/lib $(DESTDIR)/usr/include/bogl
 	install -m644 $(SHARED_LIB) $(DESTDIR)/usr/lib/$(SHARED_LIB)
 	ln -s $(SHARED_LIB) $(DESTDIR)/usr/lib/$(DEVLINK)
 	ln -s $(SHARED_LIB) $(DESTDIR)/usr/lib/$(SONAME)
 	install -m644 $(LIB) $(DESTDIR)/usr/lib/$(LIB)
 	install -m644 *.h $(DESTDIR)/usr/include/bogl
-	install -m755 bdftobogl mergebdf bterm pngtobogl reduce-font $(DESTDIR)/usr/bin
+	install -m755 bdftobogl bterm pngtobogl $(DESTDIR)/usr/bin
 	install -d $(DESTDIR)/usr/share/terminfo
 	tic -o$(DESTDIR)/usr/share/terminfo bterm.ti
+endif
+	install -m755 mergebdf reduce-font $(DESTDIR)/usr/bin
