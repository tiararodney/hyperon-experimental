dnl configure.ac -- check system-level build dependencies for hyperon-experimental
dnl
dnl Usage:
dnl   autoreconf -i
dnl   ./configure

AC_PREREQ([2.69])
AC_INIT([hyperon-experimental-deps], [0.2.10], [https://github.com/trueagi-io/hyperon-experimental/issues])
AC_CONFIG_SRCDIR([Cargo.toml])

dnl Accumulate missing dependencies and report them all at the end.
missing_deps=""
append_missing() {
    missing_deps="$missing_deps
  - $1"
}

dnl ---------------------------------------------------------------------------
dnl Rust toolchain (installed via rustup, not via cargo itself)
dnl ---------------------------------------------------------------------------
AC_PATH_PROG([CARGO], [cargo], [no])
AS_IF([test "x$CARGO" = "xno"],
      [append_missing "cargo (install Rust: https://www.rust-lang.org/tools/install)"])

AC_PATH_PROG([RUSTC], [rustc], [no])
AS_IF([test "x$RUSTC" = "xno"],
      [append_missing "rustc (install Rust: https://www.rust-lang.org/tools/install)"])

dnl ---------------------------------------------------------------------------
dnl C compiler, GCC >= 7.5 when GCC is used
dnl ---------------------------------------------------------------------------
AC_PROG_CC
AS_IF([test "x$GCC" = "xyes"],
      [gcc_version=`$CC -dumpfullversion -dumpversion 2>/dev/null`
       AC_MSG_CHECKING([whether GCC version >= 7.5])
       AS_VERSION_COMPARE([$gcc_version], [7.5],
           [AC_MSG_RESULT([no ($gcc_version)])
            append_missing "GCC >= 7.5 (found $gcc_version)"],
           [AC_MSG_RESULT([yes ($gcc_version)])],
           [AC_MSG_RESULT([yes ($gcc_version)])])])

dnl ---------------------------------------------------------------------------
dnl CMake >= 3.24
dnl ---------------------------------------------------------------------------
AC_PATH_PROG([CMAKE], [cmake], [no])
AS_IF([test "x$CMAKE" = "xno"],
      [append_missing "cmake >= 3.24"],
      [cmake_version=`$CMAKE --version | head -n1 | sed 's/@<:@^0-9@:>@*\(@<:@0-9.@:>@*\).*/\1/'`
       AC_MSG_CHECKING([whether CMake version >= 3.24])
       AS_VERSION_COMPARE([$cmake_version], [3.24],
           [AC_MSG_RESULT([no ($cmake_version)])
            append_missing "cmake >= 3.24 (found $cmake_version)"],
           [AC_MSG_RESULT([yes ($cmake_version)])],
           [AC_MSG_RESULT([yes ($cmake_version)])])])

dnl ---------------------------------------------------------------------------
dnl Python3 >= 3.8 with development headers
dnl ---------------------------------------------------------------------------
AC_PATH_PROG([PYTHON3], [python3], [no])
AS_IF([test "x$PYTHON3" = "xno"],
      [append_missing "python3 >= 3.8"],
      [python_version=`$PYTHON3 -c 'import sys; print("%d.%d" % sys.version_info@<:@:2@:>@)'`
       AC_MSG_CHECKING([whether Python version >= 3.8])
       AS_VERSION_COMPARE([$python_version], [3.8],
           [AC_MSG_RESULT([no ($python_version)])
            append_missing "python3 >= 3.8 (found $python_version)"],
           [AC_MSG_RESULT([yes ($python_version)])],
           [AC_MSG_RESULT([yes ($python_version)])])

       AC_MSG_CHECKING([for Python development headers (Python.h)])
       python_include=`$PYTHON3 -c 'import sysconfig; print(sysconfig.get_path("include"))' 2>/dev/null`
       AS_IF([test -n "$python_include" && test -f "$python_include/Python.h"],
             [AC_MSG_RESULT([yes ($python_include)])],
             [AC_MSG_RESULT([no])
              append_missing "python3-dev / Python.h (Debian: apt-get install python3-dev)"])])

dnl ---------------------------------------------------------------------------
dnl Protobuf compiler (required by DAS integration / metta-bus-client)
dnl ---------------------------------------------------------------------------
AC_PATH_PROG([PROTOC], [protoc], [no])
AS_IF([test "x$PROTOC" = "xno"],
      [append_missing "protoc (Debian: apt-get install protobuf-compiler)"])

dnl ---------------------------------------------------------------------------
dnl OpenSSL and zlib libraries (required by Git based modules support)
dnl ---------------------------------------------------------------------------
AC_CHECK_HEADER([openssl/ssl.h], [],
      [append_missing "OpenSSL headers (Debian: apt-get install libssl-dev)"])
AC_CHECK_LIB([ssl], [SSL_CTX_new], [],
      [append_missing "libssl (Debian: apt-get install libssl-dev)"])
AC_CHECK_LIB([crypto], [EVP_EncryptInit_ex], [],
      [append_missing "libcrypto (Debian: apt-get install libssl-dev)"])

AC_CHECK_HEADER([zlib.h], [],
      [append_missing "zlib headers (Debian: apt-get install zlib1g-dev)"])
AC_CHECK_LIB([z], [inflate], [],
      [append_missing "libz (Debian: apt-get install zlib1g-dev)"])

dnl ---------------------------------------------------------------------------
dnl Report
dnl ---------------------------------------------------------------------------
AS_IF([test -n "$missing_deps"],
      [AC_MSG_ERROR([missing system dependencies:$missing_deps])],
      [AC_MSG_NOTICE([all system dependencies found])])

AC_OUTPUT
