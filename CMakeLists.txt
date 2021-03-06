#
# RouterKeygen build system
# Author: Rui Araújo <ruka.araujo AT gmail.com>
#

# Project Info
SET(PROJECT_NAME_SHORT   "RouterKeygen")
SET(PROJECT_NAME_LONG    "RouterKeygen")
SET(PROJECT_DESCRIPTION  "Default WPA/WEP key generator for several routers such as Thomson based routers ( this includes Thomson, SpeedTouch, Orange, Infinitum, BBox, DMax, BigPond, O2Wireless, Otenet, Cyta, TN_private and Blink ), DLink ( only some models ), Pirelli Discus, Eircom, Verizon FiOS ( only some routers supported), Alice AGPF, FASTWEB Pirelli and Telsey, Huawei (some InfinitumXXXX), Wlan_XXXX or Jazztel_XXXX, Wlan_XX ( only some are supported), Ono ( P1XXXXXX0000X ), WlanXXXXXX YacomXXXXXX and WifiXXXXXX, Sky V1 routers, Clubinternet.box v1 and v2 ( TECOM-AH4XXXX ), InfostradaWifi, CONN-X, Megared, EasyBox Arcor and Vodafone, PBS (Austria), MAXCOM, PTV, TeleTu/Tele2, Axtel Axtel-xtremo, Intercable, OTE, Cabovisao Sagem, Alice in Germany and Speedport.")
SET(PROJECT_COPYRIGHT    "Copyright (C) 2010-2013 Rui Araújo")
SET(PROJECT_CONTACT      "ruka.araujo@gmail.com")
SET(PROJECT_VENDOR       "Rui Araújo")
SET(ORG_WEBSITE          "http://code.google.com/p/android-thomson-key-solver/")

# Configuring build type
IF(NOT CMAKE_BUILD_TYPE)
    SET(CMAKE_BUILD_TYPE "Debug" CACHE STRING
        "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel."
        FORCE)
    MESSAGE("Setting build type to '${CMAKE_BUILD_TYPE}'")
ELSE(NOT CMAKE_BUILD_TYPE)
    MESSAGE("Build type set to '${CMAKE_BUILD_TYPE}'")
ENDIF(NOT CMAKE_BUILD_TYPE)

IF(WIN32)
    # Building in release for WIN32
    SET(CMAKE_BUILD_TYPE "Release")
    SET_PROPERTY(DIRECTORY APPEND PROPERTY COMPILE_DEFINITIONS_RELEASE QT_NO_DEBUG)
    # Use mingw's sprintf instead of windows's
    ADD_DEFINITIONS( -D__USE_MINGW_ANSI_STDIO=1 )
ENDIF(WIN32)

# Project Version
SET(CPACK_PACKAGE_VERSION_MAJOR "1")
SET(CPACK_PACKAGE_VERSION_MINOR "0")
SET(CPACK_PACKAGE_VERSION_PATCH "0")
SET(CPACK_PACKAGE_VERSION ${CPACK_PACKAGE_VERSION_MAJOR}.${CPACK_PACKAGE_VERSION_MINOR}.${CPACK_PACKAGE_VERSION_PATCH})
SET(PROJECT_VERSION_MAJOR ${CPACK_PACKAGE_VERSION_MAJOR}.${CPACK_PACKAGE_VERSION_MINOR})
SET(PROJECT_VERSION ${CPACK_PACKAGE_VERSION})

MESSAGE(STATUS "Version: ${PROJECT_VERSION}")

STRING(TOLOWER ${PROJECT_NAME_SHORT} PROJECT_NAME_SHORT_LOWER)
PROJECT(${PROJECT_NAME_SHORT_LOWER}-${PROJECT_VERSION})

# System on which project is built
SITE_NAME(HOSTNAME)
SET(SYSNAME ${CMAKE_SYSTEM}-${CMAKE_SYSTEM_PROCESSOR})

# CMake Settings
CMAKE_MINIMUM_REQUIRED(VERSION 2.6.0)
SET(CMAKE_COLOR_MAKEFILE ON)
SET(CMAKE_VERBOSE_MAKEFILE OFF)

# Set path to additional CMake modules
SET(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake ${CMAKE_MODULE_PATH})

IF(APPLE)
    SET(CMAKE_OSX_ARCHITECTURES x86_64 )
ENDIF(APPLE)

IF (UNIX)
    SET(WITH_PROFILING FALSE CACHE BOOL "Build with profiling support" )
    SET(CMAKE_INSTALL_PREFIX /usr)
ENDIF(UNIX)

# Search and include Qt4 parameters
# Set Qt min required version and add modules here
FIND_PACKAGE(Qt4 4.6.0 COMPONENTS QtCore QtGui QtNetwork REQUIRED )
FIND_PACKAGE(OpenSSL REQUIRED )

#FindQt4.cmake is so brain-dead when Xcompiling
SET(ROUTERKEYGEN_LIBS ${QT_QTSCRIPT_LIBRARY}  ${QT_QTNETWORK_LIBRARY} ${QT_QTGUI_LIBRARY} ${QT_QTCORE_LIBRARY} ${OPENSSL_LIBRARIES} ) # Add here any other QT module or other library
INCLUDE(${QT_USE_FILE})
ADD_DEFINITIONS(${QT_DEFINITIONS})
IF(WIN32 AND CMAKE_CROSS_COMPILING)
    SET (ROUTERKEYGEN_LIBS ${ROUTERKEYGEN_LIBS} crypt32 jpeg png tiff mng lzma lcms2 z imm32 winmm ws2_32 )
ENDIF(WIN32 AND CMAKE_CROSS_COMPILING)

# Layout of CMakeQt installation
SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# Set common dirs for MAC and UNIX
SET(DEFAULT_BIN_SUBDIR     bin)
SET(DEFAULT_LIB_SUBDIR     lib)
SET(DEFAULT_DATA_SUBDIR    share)
SET(DEFAULT_SRC_SUBDIR     src)
SET(DEFAULT_INCLUDE_SUBDIR include)

# Set ROUTERKEYGEN path params
SET(ROUTERKEYGEN_BIN_SUBDIR     "${DEFAULT_BIN_SUBDIR}"        CACHE STRING "Subdirectory where executables will be installed")
SET(ROUTERKEYGEN_LIB_SUBDIR     "${DEFAULT_LIB_SUBDIR}"        CACHE STRING "Subdirectory where libraries will be installed")
SET(ROUTERKEYGEN_DATA_SUBDIR    "${DEFAULT_DATA_SUBDIR}"       CACHE STRING "Subdirectory where ROUTERKEYGEN's data will be installed")
SET(ROUTERKEYGEN_SRC_SUBDIR     "${DEFAULT_SRC_SUBDIR}"        CACHE STRING "Subdirectory where ROUTERKEYGEN's source is")
SET(ROUTERKEYGEN_INCLUDE_SUBDIR "${DEFAULT_INCLUDE_SUBDIR}"    CACHE STRING "Subdirectory where header files will be installed")

# Full paths for the installation
SET(ROUTERKEYGEN_BIN_DIR     ${CMAKE_INSTALL_PREFIX}/${ROUTERKEYGEN_BIN_SUBDIR})
SET(ROUTERKEYGEN_LIB_DIR     ${CMAKE_INSTALL_PREFIX}/${ROUTERKEYGEN_LIB_SUBDIR})
SET(ROUTERKEYGEN_INCLUDE_DIR ${CMAKE_INSTALL_PREFIX}/${ROUTERKEYGEN_INCLUDE_SUBDIR})
SET(ROUTERKEYGEN_DATA_DIR    ${CMAKE_INSTALL_PREFIX}/${ROUTERKEYGEN_DATA_SUBDIR}/routerkeygen/)
SET(ROUTERKEYGEN_DOC_DIR     ${CMAKE_INSTALL_PREFIX}/${ROUTERKEYGEN_DATA_SUBDIR}/doc/routerkeygen/)
SET(ROUTERKEYGEN_EFFECTS_DIR ${CMAKE_INSTALL_PREFIX}/${ROUTERKEYGEN_LIB_SUBDIR}/routerkeygen/effects/)
SET(ROUTERKEYGEN_DESKTOP_DIR ${CMAKE_INSTALL_PREFIX}/${ROUTERKEYGEN_DATA_SUBDIR}/applications/)
SET(ROUTERKEYGEN_PIXMAPS_DIR ${CMAKE_INSTALL_PREFIX}/${ROUTERKEYGEN_DATA_SUBDIR}/pixmaps/)

# Manual page - makes sense only on unix systems
IF(UNIX)
    SET(DEFAULT_MANUAL_SUBDIR  ${ROUTERKEYGEN_DATA_SUBDIR}/man)
    SET(ROUTERKEYGEN_MANUAL_SUBDIR  "${DEFAULT_MANUAL_SUBDIR}"  CACHE STRING "Subdirectory where manual files will be installed")
    MARK_AS_ADVANCED(ROUTERKEYGEN_MANUAL_SUBDIR)
    SET(ROUTERKEYGEN_MANUAL_DIR  ${CMAKE_INSTALL_PREFIX}/${ROUTERKEYGEN_MANUAL_SUBDIR})
ENDIF(UNIX)

# Recursively add sub dirs
SUBDIRS(lang)
SUBDIRS(src)
SUBDIRS(nsis)

# Copy stuff to doc subdirectory
INSTALL (FILES AUTHORS COPYING INSTALL NEWS README doc/routerkeygen.1
         DESTINATION ${ROUTERKEYGEN_DOC_DIR})

# Copy .desktop file
INSTALL (FILES share/routerkeygen.desktop DESTINATION ${ROUTERKEYGEN_DESKTOP_DIR})

# Copy the ROUTERKEYGEN pixmap
INSTALL (FILES share/routerkeygen.png DESTINATION ${ROUTERKEYGEN_PIXMAPS_DIR})

#############################################################
# Enable packaging

INCLUDE(InstallRequiredSystemLibraries)

SET(CPACK_STRIP_FILES "TRUE")
SET(CPACK_SYSTEM_NAME ${CMAKE_SYSTEM_NAME})
SET(CPACK_PACKAGE_NAME ${PROJECT_NAME_SHORT})
SET(CPACK_PACKAGE_DESCRIPTION "${PROJECT_NAME_LONG}")
SET(CPACK_PACKAGE_DESCRIPTION_SUMMARY "${PROJECT_NAME_SHORT} \n\\ ${PROJECT_DESCRIPTION}")
SET(CPACK_PACKAGE_VENDOR ${PROJECT_VENDOR})
SET(CPACK_PACKAGE_CONTACT ${PROJECT_CONTACT})
SET(CPACK_PACKAGE_DESCRIPTION_FILE "${CMAKE_SOURCE_DIR}/README")
SET(CPACK_RESOURCE_FILE_README ${CMAKE_SOURCE_DIR}/README)
SET(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_SOURCE_DIR}/COPYING")
SET(CPACK_PACKAGE_INSTALL_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}")

# Force Package Name
SET(CPACK_PACKAGE_FILE_NAME ${PROJECT_NAME}-${CPACK_SYSTEM_NAME}-${CMAKE_SYSTEM_PROCESSOR})

# NSIS Windows installer
IF(WIN32 AND NOT UNIX)
    # There is a bug in NSI that does not handle full unix paths properly. Make
    # sure there is at least one set of four (4) backslashes.
    SET(CPACK_PACKAGE_ICON "${CMAKE_SOURCE_DIR}/share\\\\routerkeygen.png")
    SET(CPACK_NSIS_INSTALLED_ICON_NAME "\\\\routerkeygen.exe")
    SET(CPACK_NSIS_DISPLAY_NAME "${CPACK_PACKAGE_INSTALL_DIRECTORY} ROUTERKEYGEN")
    SET(CPACK_NSIS_HELP_LINK "http:\\\\\\\\code.google.com/p/android-thomson-key-solver/")
    SET(CPACK_NSIS_URL_INFO_ABOUT "http:\\\\\\\\code.google.com/p/android-thomson-key-solver/")
    SET(CPACK_NSIS_CONTACT "ruka.araujo AT gmail.com")
    SET(CPACK_NSIS_MODIFY_PATH ON)
ENDIF(WIN32 AND NOT UNIX)

# RPM packages
INCLUDE(${CMAKE_MODULE_PATH}/RpmBuild.cmake)
IF(RPMBUILD_FOUND AND NOT WIN32)
    SET(CPACK_GENERATOR "RPM")
    SET(CPACK_RPM_PACKAGE_SUMMARY ${CPACK_PACKAGE_DESCRIPTION_SUMMARY})
    SET(CPACK_RPM_PACKAGE_NAME ${CPACK_PACKAGE_NAME})
    SET(CPACK_RPM_PACKAGE_VERSION ${PROJECT_VERSION})
    SET(CPACK_RPM_PACKAGE_LICENSE "GPLv3+ License")
    SET(CPACK_RPM_PACKAGE_GROUP ${PROJECT_VENDOR})
    SET(CPACK_RPM_PACKAGE_VENDOR ${PROJECT_VENDOR})
    SET(CPACK_RPM_PACKAGE_SUMMARY ${PROJECT_DESCRIPTION})
    SET(CPACK_RPM_PACKAGE_DEPENDS "libc, qt ( >= 4.6 ), qt-x11 ( >= 4.6 ), NetworkManager ( >= 0.8 )") # Specify dependencies here
    SET(CPACK_SET_DESTDIR TRUE)
ENDIF(RPMBUILD_FOUND AND NOT WIN32)

# Debian packages
INCLUDE (${CMAKE_MODULE_PATH}/DpkgBuild.cmake)
IF(DPKG_FOUND AND NOT WIN32)
    SET(CPACK_GENERATOR "DEB")
    SET(CPACK_DEBIAN_PACKAGE_NAME ${CPACK_PACKAGE_NAME})
    SET(CPACK_DEBIAN_PACKAGE_VERSION ${CPACK_PACKAGE_VERSION})
    SET(CPACK_DEBIAN_PACKAGE_DESCRIPTION ${CPACK_PACKAGE_DESCRIPTION_SUMMARY})
    SET(CPACK_DEBIAN_PACKAGE_MAINTAINER "${CPACK_PACKAGE_VENDOR} <${CPACK_PACKAGE_CONTACT}>")
    SET(CPACK_DEBIAN_PACKAGE_PRIORITY "optional")
    SET(CPACK_DEBIAN_PACKAGE_DEBUG ON)
    SET(CPACK_DEBIAN_PACKAGE_DEPENDS ${PACKAGE_REQUIRES})
    SET(CPACK_DEBIAN_PACKAGE_DEPENDS "libc6 (>= 2.3.1-6), l, libqt4-gui ( >= 4.6 ), libqt4-network ( >= 4.6 ), network-manager ( >= 0.8 )") # Specify dependencies here
    SET(CPACK_SET_DESTDIR TRUE)
ENDIF(DPKG_FOUND AND NOT WIN32)

SET(CPACK_PACKAGE_EXECUTABLES "routerkeygen" "ROUTERKEYGEN")
INCLUDE(CPack)

#For debug purposes, print all variables
#get_cmake_property(_variableNames VARIABLES)
#foreach (_variableName ${_variableNames})
#    message(STATUS "${_variableName}=${${_variableName}}")
#endforeach()
