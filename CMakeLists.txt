# version >= 2.8.6 is needed because of CMAKE_AUTOMOC feature
cmake_minimum_required(VERSION 2.8.6)
project(pcmanfm-qt)

# Setup common compiler flags/variables ---------------------------------------
set(PCMANFM_VERSION "0.1")
set(LIBFM_QT_VERSION "0.1")

find_package(Qt4 4.6 REQUIRED QtCore QtGui QtDBus)
include(${QT_USE_FILE})

find_package(PkgConfig)
pkg_check_modules(LIBFM REQUIRED
  glib-2.0
  gio-2.0
  gio-unix-2.0
  libfm>=1.2.0
#  libstartup-notification-1.0
  x11
)

include(GNUInstallDirs)

# do not use Qt keywords
add_definitions(
  -DQT_NO_KEYWORDS
)

if (CMAKE_COMPILER_IS_GNUCXX)
  # set visibility to hidden to hide symbols, unlesss they're exporeted manually in the code
  set(CMAKE_CXX_FLAGS "-fvisibility=hidden -fvisibility-inlines-hidden -fno-exceptions")
endif()


# Eanble CMake auto-moc support for Qt
set(CMAKE_AUTOMOC TRUE)

# The core library libfm-qt ---------------------------------------------------
add_subdirectory(libfm-qt)

# The main program pcmanfm ----------------------------------------------------
add_subdirectory(pcmanfm)

# update translations
add_custom_target(update_translations ALL DEPENDS
  libfm_translations
  pcmanfm_translations
)

# building tarball with CPack -------------------------------------------------
# To create a source distribution, type:
# make package_source
include (InstallRequiredSystemLibraries)
set (CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/COPYING")
set (CPACK_RESOURCE_FILE_README "${CMAKE_CURRENT_SOURCE_DIR}/README")
set (CPACK_PACKAGE_VENDOR "")
set (CPACK_PACKAGE_VERSION_MAJOR "0")
set (CPACK_PACKAGE_VERSION_MINOR "1")
set (CPACK_PACKAGE_VERSION_PATCH "0")
set (CPACK_GENERATOR TBZ2)
set (CPACK_SOURCE_GENERATOR TBZ2)
set (CPACK_SOURCE_IGNORE_FILES /build/;.gitignore;.*~;.git;.kdev4;temp)
# set (CPACK_SOURCE_PACKAGE_FILE_NAME pcmanfm-qt.0.1.0.tar.gz)
include (CPack)
