AC_PREREQ([2.69])
AC_INIT([openpower-pnor-code-mgmt], [1.0], [https://github.com/openbmc/openpower-pnor-code-mgmt/issues])
AC_LANG([C++])
AC_CONFIG_HEADERS([config.h])
AM_INIT_AUTOMAKE([subdir-objects -Wall -Werror foreign dist-xz])
AM_SILENT_RULES([yes])

AC_CONFIG_MACRO_DIRS([m4])

# Checks for programs
AC_PROG_CXX
AC_PROG_INSTALL #Checks/sets the install variable to be used
AC_PROG_MAKE_SET

# Check for libraries
PKG_CHECK_MODULES([PHOSPHOR_DBUS_INTERFACES], [phosphor-dbus-interfaces])
PKG_CHECK_MODULES([SDBUSPLUS], [sdbusplus])
PKG_CHECK_MODULES([PHOSPHOR_LOGGING], [phosphor-logging])

AC_PATH_PROG([SDBUSPLUSPLUS], [sdbus++])

# Checks for library functions
LT_INIT # Required for systemd linking

PKG_PROG_PKG_CONFIG
AC_ARG_WITH([systemdsystemunitdir],
     [AS_HELP_STRING([--with-systemdsystemunitdir=DIR], [Directory for systemd service files])],,
     [with_systemdsystemunitdir=auto])
AS_IF([test "x$with_systemdsystemunitdir" = "xyes" -o "x$with_systemdsystemunitdir" = "xauto"], [
     def_systemdsystemunitdir=$($PKG_CONFIG --variable=systemdsystemunitdir systemd)

     AS_IF([test "x$def_systemdsystemunitdir" = "x"],
   [AS_IF([test "x$with_systemdsystemunitdir" = "xyes"],
    [AC_MSG_ERROR([systemd support requested but pkg-config unable to query systemd package])])
    with_systemdsystemunitdir=no],
   [with_systemdsystemunitdir="$def_systemdsystemunitdir"])])
AS_IF([test "x$with_systemdsystemunitdir" != "xno"],
      [AC_SUBST([systemdsystemunitdir], [$with_systemdsystemunitdir])])
AM_CONDITIONAL([HAVE_SYSTEMD], [test "x$with_systemdsystemunitdir" != "xno"])

# Check/set gtest specific functions.
AX_PTHREAD([GTEST_CPPFLAGS="-DGTEST_HAS_PTHREAD=1"],[GTEST_CPPFLAGS="-DGTEST_HAS_PTHREAD=0"])
AC_SUBST(GTEST_CPPFLAGS)
AC_ARG_ENABLE([oe-sdk],
    AS_HELP_STRING([--enable-oe-sdk], [Link testcases absolutely against OE SDK so they can be ran within it.])
)
AC_ARG_VAR(OECORE_TARGET_SYSROOT,
    [Path to the OE SDK SYSROOT])
AS_IF([test "x$enable_oe_sdk" == "xyes"],
    AS_IF([test "x$OECORE_TARGET_SYSROOT" == "x"],
          AC_MSG_ERROR([OECORE_TARGET_SYSROOT must be set with --enable-oe-sdk])
    )
    AC_MSG_NOTICE([Enabling OE-SDK at $OECORE_TARGET_SYSROOT])
    [
        testcase_flags="-Wl,-rpath,\${OECORE_TARGET_SYSROOT}/lib"
        testcase_flags="${testcase_flags} -Wl,-rpath,\${OECORE_TARGET_SYSROOT}/usr/lib"
        testcase_flags="${testcase_flags} -Wl,-dynamic-linker,`find \${OECORE_TARGET_SYSROOT}/lib/ld-*.so | sort -r -n | head -n1`"
    ]
    AC_SUBST([OESDK_TESTCASE_FLAGS], [$testcase_flags])
)

# Checks for typedefs, structures, and compiler characteristics.
AX_CXX_COMPILE_STDCXX([17], [noext], [mandatory])
AX_APPEND_COMPILE_FLAGS([-Wall -Werror], [CXXFLAGS])

AC_ARG_VAR(SOFTWARE_OBJPATH, [The software manager Dbus root])
AS_IF([test "x$SOFTWARE_OBJPATH" == "x"], [SOFTWARE_OBJPATH="/xyz/openbmc_project/software"])
AC_DEFINE_UNQUOTED([SOFTWARE_OBJPATH], ["$SOFTWARE_OBJPATH"], [The software manager Dbus root])

AC_DEFINE(ACTIVATION_FWD_ASSOCIATION, "inventory", [The name of the activation's forward association.])
AC_DEFINE(ACTIVATION_REV_ASSOCIATION, "activation", [The name of the activation's reverse association.])
AC_DEFINE(HOST_INVENTORY_PATH, "/xyz/openbmc_project/inventory/system/chassis", [The host inventory path.])

AC_DEFINE(ACTIVE_FWD_ASSOCIATION, "active", [The name of the active's forward association.])
AC_DEFINE(ACTIVE_REV_ASSOCIATION, "software_version", [The name of the active's reverse association.])

AC_DEFINE(FUNCTIONAL_FWD_ASSOCIATION, "functional", [The name of the functional forward association.])
AC_DEFINE(FUNCTIONAL_REV_ASSOCIATION, "software_version", [The functional reverse association.])

AC_DEFINE(VERSION_IFACE, "xyz.openbmc_project.Software.Version",
    [The software version manager interface])
AC_DEFINE(FILEPATH_IFACE, "xyz.openbmc_project.Common.FilePath",
    [The common file path interface])

AC_DEFINE(BUSNAME_UPDATER, "org.open_power.Software.Host.Updater",
    [The item updater DBus busname to own.])

AC_ARG_VAR(IMG_DIR, [Directory where downloaded or uploaded software images are placed and extracted])
AS_IF([test "x$IMG_DIR" == "x"], [IMG_DIR="/tmp/images"])
AC_DEFINE_UNQUOTED([IMG_DIR], ["$IMG_DIR"], [Directory where downloaded or uploaded software images are placed and extracted])

AC_ARG_VAR(MANIFEST_FILE, [The path to the MANIFEST file])
AS_IF([test "x$MANIFEST_FILE" == "x"], [MANIFEST_FILE="MANIFEST"])
AC_DEFINE_UNQUOTED([MANIFEST_FILE], ["$MANIFEST_FILE"], [The path to the MANIFEST file])

AC_ARG_VAR(PUBLICKEY_FILE_NAME, [The name of the public key file])
AS_IF([test "x$PUBLICKEY_FILE_NAME" == "x"], [PUBLICKEY_FILE_NAME="publickey"])
AC_DEFINE_UNQUOTED([PUBLICKEY_FILE_NAME], ["$PUBLICKEY_FILE_NAME"], [The name of the public key file])

AC_ARG_VAR(HASH_FILE_NAME, [Hash file name])
AS_IF([test "x$HASH_FILE_NAME" == "x"], [HASH_FILE_NAME="hashfunc"])
AC_DEFINE_UNQUOTED([HASH_FILE_NAME], ["$HASH_FILE_NAME"], [The name of the hash file])

AC_ARG_VAR(PNOR_SIGNED_IMAGE_CONF_PATH, [Path of PNOR image public key and hash function files])
AS_IF([test "x$PNOR_SIGNED_IMAGE_CONF_PATH" == "x"], [PNOR_SIGNED_IMAGE_CONF_PATH="/etc/activationdata/"])
AC_DEFINE_UNQUOTED([PNOR_SIGNED_IMAGE_CONF_PATH], ["$PNOR_SIGNED_IMAGE_CONF_PATH"], [Path of PNOR image public key and hash function files])

AC_ARG_VAR(SIGNATURE_FILE_EXT, [The extension of the Signature file])
AS_IF([test "x$SIGNATURE_FILE_EXT" == "x"], [SIGNATURE_FILE_EXT=".sig"])
AC_DEFINE_UNQUOTED([SIGNATURE_FILE_EXT], ["$SIGNATURE_FILE_EXT"], [The extension of the Signature file])

# setup signature verification
AC_ARG_ENABLE([verify_pnor_signature],
    AS_HELP_STRING([--enable-verify_pnor_signature], [Enable image signature validation.]))
AS_IF([test "x$enable_verify_pnor_signature" == "xyes"], \
    [AC_DEFINE([WANT_SIGNATURE_VERIFY],[],[Enable image signature validation.])])
AM_CONDITIONAL([WANT_SIGNATURE_VERIFY_BUILD], [test "x$enable_verify_pnor_signature" == "xyes"])

AC_DEFINE(CHASSIS_STATE_PATH, "/xyz/openbmc_project/state/chassis0",
    [The chassis state path.])
AC_DEFINE(CHASSIS_STATE_OBJ, "xyz.openbmc_project.State.Chassis",
    [The chassis state interface.])
AC_DEFINE(CHASSIS_STATE_OFF, "xyz.openbmc_project.State.Chassis.PowerState.Off",
    [The chassis state off property value.])

AC_DEFINE(MAPPER_BUSNAME, "xyz.openbmc_project.ObjectMapper",
    [The object mapper busname.])
AC_DEFINE(MAPPER_PATH, "/xyz/openbmc_project/object_mapper",
    [The object mapper path.])
AC_DEFINE(MAPPER_INTERFACE, "xyz.openbmc_project.ObjectMapper",
    [The object mapper interface.])

AC_DEFINE(SYSTEMD_BUSNAME, "org.freedesktop.systemd1",
    [systemd busname.])
AC_DEFINE(SYSTEMD_PATH, "/org/freedesktop/systemd1",
    [systemd path.])
AC_DEFINE(SYSTEMD_INTERFACE, "org.freedesktop.systemd1.Manager",
    [systemd interface.])
AC_DEFINE(SYSTEMD_PROPERTY_INTERFACE, "org.freedesktop.DBus.Properties",
    [systemd properties interface.])
AC_DEFINE(ASSOCIATIONS_INTERFACE, "org.openbmc.Associations",
    [The associations interface.])

AC_DEFINE(PNOR_TOC_FILE, "pnor.toc",
    [The name of the PNOR table of contents file])
AC_DEFINE(MEDIA_DIR, "/media/",
    [The base dir where all PNOR RO AND RW partitions are mounted])
AC_DEFINE(PERSIST_DIR, "/var/lib/obmc/openpower-pnor-code-mgmt/",
    [The dir where activation data is stored in files])
AC_DEFINE(PNOR_RO_PREFIX, "/media/pnor-ro-",
    [The prefix path for the versioned read-only pnor partitions])
AC_DEFINE(PNOR_RW_PREFIX, "/media/pnor-rw-",
    [The prefix path for the versioned read-write pnor partitions])
AC_DEFINE(PNOR_PRSV, "/media/pnor-prsv",
    [The path for the preserved pnor partitions])
AC_DEFINE(PNOR_ACTIVE_PATH, "/var/lib/phosphor-software-manager/pnor/",
    [Path to the active pnor partitions])
AC_DEFINE(PNOR_RO_ACTIVE_PATH, "/var/lib/phosphor-software-manager/pnor/ro",
    [Path to the active read-only pnor partitions])
AC_DEFINE(PNOR_RW_ACTIVE_PATH, "/var/lib/phosphor-software-manager/pnor/rw",
    [Path to the active read-write pnor partitions])
AC_DEFINE(PNOR_PRSV_ACTIVE_PATH, "/var/lib/phosphor-software-manager/pnor/prsv",
    [Path to the active preserved pnor partitions])

# Setup ubifs layout support
# For now make it default enabled
AC_ARG_ENABLE([ubifs_layout],
    AS_HELP_STRING([--disable-ubifs_layout], [Disable ubifs support.]))
AS_IF([test "x$enable_ubifs_layout" != "xno"], \
    [AC_DEFINE([UBIFS_LAYOUT],[],[Enable ubifs support.])])
AM_CONDITIONAL([UBIFS_LAYOUT], [test "x$enable_ubifs_layout" != "xno"])

AC_ARG_VAR(ACTIVE_PNOR_MAX_ALLOWED, [The maximum allowed active pnor versions])
AS_IF([test "x$ACTIVE_PNOR_MAX_ALLOWED" == "x"], [ACTIVE_PNOR_MAX_ALLOWED=2])
AC_DEFINE_UNQUOTED([ACTIVE_PNOR_MAX_ALLOWED], [$ACTIVE_PNOR_MAX_ALLOWED], [The maximum allowed active pnor versions])

AC_ARG_VAR(PNOR_MSL, [The PNOR minimum ship level])
AS_IF([test "x$PNOR_MSL" == "x"], [PNOR_MSL=""])
AC_DEFINE_UNQUOTED([PNOR_MSL], ["$PNOR_MSL"], [The PNOR minimum ship level])

AC_ARG_VAR(PNOR_VERSION_PARTITION, [The name of the PNOR version partition])
AS_IF([test "x$PNOR_VERSION_PARTITION" == "x"], [PNOR_VERSION_PARTITION="VERSION"])
AC_DEFINE_UNQUOTED([PNOR_VERSION_PARTITION], ["$PNOR_VERSION_PARTITION"],
    [The name of the PNOR version partition])

AC_CONFIG_FILES([Makefile test/Makefile])
AC_OUTPUT
