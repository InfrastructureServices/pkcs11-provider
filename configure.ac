#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ([2.71])
AC_INIT([pkcs11-provider], [0.1], [simo@redhat.com])
AC_CONFIG_SRCDIR([src/provider.c])
AC_CONFIG_HEADERS([src/config.h])
AM_INIT_AUTOMAKE([foreign dist-xz -Wall -Werror])
AC_CONFIG_MACRO_DIRS([m4])

# Checks for programs.
AC_PROG_CC
AM_PROG_AR
LT_INIT
PKG_PROG_PKG_CONFIG

AX_CHECK_COMPILE_FLAG([-std=c11],
                      [CFLAGS="$CFLAGS -std=c11"],
                      AC_MSG_ERROR([C compiler must support at least C11 standard])
)

STD_CFLAGS="-Wall -Wextra -Wwrite-strings -Wpointer-arith -Wno-missing-field-initializers -Wformat -Wshadow"

# Temporarily disable unused parameter until the implementation is complete
STD_CFLAGS="$STD_CFLAGS -Wno-unused-parameter"

# These should be always errors
STD_CFLAGS="$STD_CFLAGS -Werror=implicit-function-declaration -Werror=missing-prototypes -Werror=format-security -Werror=parentheses -Werror=implicit -Werror=strict-prototypes"

# Don't enable warnings on VLA yet, but I would avoid using VLAs
# STD_CFLAGS"$STD_CFLAGS -Werror=vla"

AX_CHECK_COMPILE_FLAG([-fno-strict-aliasing],
		      [STD_CFLAGS="$STD_CFLAGS -fno-strict-aliasing"])
AX_CHECK_COMPILE_FLAG([-Werror -fno-delete-null-pointer-checks],
		      [STD_CFLAGS="$STD_CFLAGS -fno-delete-null-pointer-checks"])
AX_CHECK_COMPILE_FLAG([-fdiagnostics-show-option],
		      [STD_CFLAGS="$STD_CFLAGS -fdiagnostics-show-option"])

AC_SUBST([STD_CFLAGS])

# Checks for libraries.
PKG_CHECK_MODULES(
	[OPENSSL],
	[libcrypto >= 3.0.0],
	,
	[AC_MSG_ERROR([libcrypto >= 3.0.0 is required])]
)
AC_SUBST([SHARED_EXT], $(eval echo "${shrext_cmds}"))

# Try nss-softoken first as used on Fedora,
# fallback to "nss" as used on Debian
PKG_CHECK_EXISTS(
        [nss-softokn],
        [PKG_CHECK_VAR([SOFTOKENDIR], [nss-softokn], [libdir])],
        [PKG_CHECK_EXISTS(
                [nss],
                [PKG_CHECK_VAR([SOFTOKENDIR], [nss], [libdir], [AC_SUBST([SOFTOKEN_SUBDIR], "nss/")])]
        )]
)

AS_IF([test "x$SOFTOKENDIR" = "x"], [
  AC_MSG_WARN([Unable to identify softoken plugin path.])
])

# Checks for header files.
AC_CHECK_HEADERS([string.h dlfcn.h])

# Checks for typedefs, structures, and compiler characteristics.
AC_CHECK_HEADER_STDBOOL
AC_C_INLINE
AC_TYPE_SIZE_T

# Checks for library functions.
AC_CHECK_FUNCS([strpbrk])

AC_CONFIG_FILES([
	Makefile
	src/Makefile
	tests/Makefile
])
AC_OUTPUT
