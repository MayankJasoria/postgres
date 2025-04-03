project('rlibv2_internal', 'cpp', # Internal name within Postgres
        version : '0.1',
        default_options : ['warning_level=3', 'cpp_std=c++17'])

cpp = meson.get_compiler('cpp')

ibverbs_dep = declare_dependency(
    include_directories: '/usr/include',
    dependencies: cpp.find_library('ibverbs', dirs: '/usr/lib')
)
gflags_dep = dependency('gflags', required: true)
threads_dep = dependency('threads', required: true)

# Include directory for core library headers
rlibv2_core_incdir = include_directories('core')

# Include directory for C wrapper headers
rlibv2_c_wrapper_incdir = include_directories('c_wrappers')

rlibv2_include_dirs = [
    rlibv2_c_wrapper_incdir,
    rlibv2_core_incdir,
    gflags_dep.get_variable('includedir')
]

# "Build" the core library (since it's header-only, this primarily defines include paths)
rlibv2_core_lib = library('rlibv2_core', # A name for the core library target
                          sources: [], # Header-only library doesn't have source files to compile directly
                          include_directories: rlibv2_include_dirs,
                          dependencies: [ibverbs_dep], # Add ibverbs_dep as a dependency
                          install: false)

# Build the C wrapper implementation
rlibv2_wrapper_impl_lib = library('rlibv2_wrapper_impl', # A name for the wrapper implementation library
                                  sources: files([
                                                     'c_wrappers/impl/rdmaio_config_c.cpp',
                                                     'c_wrappers/impl/rdmaio_impl_c.cpp',
                                                     'c_wrappers/impl/rdmaio_lib_c.cpp',
                                                     'c_wrappers/impl/rdmaio_mem_c.cpp',
                                                     'c_wrappers/impl/rdmaio_nic_c.cpp',
                                                     'c_wrappers/impl/rdmaio_rc_c.cpp',
                                                     'c_wrappers/impl/rdmaio_rc_recv_manager_c.cpp',
                                                     'c_wrappers/impl/rdmaio_recv_iter_c.cpp',
                                                     'c_wrappers/impl/rdmaio_reg_handler_c.cpp',
                                                     'c_wrappers/impl/rdmaio_rctrl_c.cpp',
                                                 ]),
                                  include_directories: [rlibv2_c_wrapper_incdir, rlibv2_core_incdir], # Include both header directories
                                  link_with: [rlibv2_core_lib], # Link the wrapper implementation against the core library
                                  dependencies: [ibverbs_dep, gflags_dep, threads_dep], # Use dependencies for external libraries
                                  install: false)

# Declare a dependency that other parts of Postgres can use
rlibv2_dep = declare_dependency(
    link_with: rlibv2_wrapper_impl_lib,
    include_directories: [rlibv2_c_wrapper_incdir, rlibv2_core_incdir] # Only local includes here
)