# ObjectStore
add_library(fio_ceph_objectstore SHARED fio_ceph_objectstore.cc)
target_link_libraries(fio_ceph_objectstore fio)

# Messenger
add_library(fio_ceph_messenger SHARED fio_ceph_messenger.cc)
target_link_libraries(fio_ceph_messenger fio)

# librgw
add_library(fio_librgw SHARED fio_librgw.cc)
target_link_libraries(fio_librgw rgw fio)

# prevent fio from adding 'typedef int bool' and gettid()
if(HAVE_GETTID)
  set(FIO_CFLAGS "-DCONFIG_HAVE_BOOL -DCONFIG_HAVE_GETTID")
else()
  set(FIO_CFLAGS "-DCONFIG_HAVE_BOOL")
endif()

# fio headers use typeof(), which requires c++11 extensions
set_target_properties(fio_ceph_objectstore PROPERTIES
  CXX_EXTENSIONS ON
  COMPILE_FLAGS "${FIO_CFLAGS}")
set_target_properties(fio_ceph_messenger PROPERTIES
  CXX_EXTENSIONS ON
  COMPILE_FLAGS "${FIO_CFLAGS}")
set_target_properties(fio_librgw PROPERTIES
  CXX_EXTENSIONS ON
  COMPILE_FLAGS "${FIO_CFLAGS}")

if(WITH_FIO)
  add_dependencies(fio_ceph_objectstore fio_ext)
  add_dependencies(fio_ceph_messenger fio_ext)
  add_dependencies(fio_librgw fio_ext)
endif()

target_link_libraries(fio_ceph_objectstore os global)
install(TARGETS fio_ceph_objectstore DESTINATION lib)

target_link_libraries(fio_ceph_messenger os global)
install(TARGETS fio_ceph_messenger DESTINATION lib)

target_link_libraries(fio_librgw os global rgw)
install(TARGETS fio_librgw DESTINATION lib)

