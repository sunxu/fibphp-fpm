import os, sys, glob
import string

def automake(path):
    os.system('''
        cd %s &&
        autoreconf --install --symlink --force &&
        ./configure --libdir=`pwd`/.libs &&
        make
    ''' % (path))


env = Environment()
home = os.getcwd()


libev_dir = home + '/libev'
libev_include = libev_dir 
libev_sh_lib = libev_dir + '/.libs/libev' + env['SHLIBSUFFIX']
libev_ar_lib = libev_dir + '/.libs/libev' + env['LIBSUFFIX']

if not os.path.isfile(libev_ar_lib):
    automake(libev_dir)


libeio_dir = home + '/libeio'
libeio_include = libeio_dir 
libeio_sh_lib = libeio_dir + '/.libs/libeio' + env['SHLIBSUFFIX']
libeio_ar_lib = libeio_dir + '/.libs/libeio' + env['LIBSUFFIX']

if not os.path.isfile(libeio_ar_lib):
    automake(libeio_dir)


libevfibers_dir = home + '/libevfibers'
libevfibers_include = libevfibers_dir 
libevfibers_sh_lib = libevfibers_dir + '/build/libevfibers' + env['SHLIBSUFFIX']
libevfibers_ar_lib = libevfibers_dir + '/build/libevfibers' + env['LIBSUFFIX']
libevfibers_cflags = {}
libevfibers_exe_linker_flags = [
    '-L ' + os.path.dirname(libev_sh_lib),
    '-L ' + os.path.dirname(libeio_sh_lib),
]

if not os.path.isfile(libevfibers_ar_lib):
    libevfibers_dir_build = libevfibers_dir + '/build'
    linker_flags = string.join(libevfibers_exe_linker_flags, ' ')
    os.system('rm -rf %s' % libevfibers_dir_build)
    os.system('mkdir %s' % libevfibers_dir_build)

    if env['PLATFORM'] == 'darwin':
        os.system('mkdir -p %s/include/linux' % libevfibers_dir_build)
        os.system('touch %s/include/linux/limits.h' % libevfibers_dir_build)
        libevfibers_cflags['CORO_PTHREAD'] = ''
        libevfibers_cflags['_XOPEN_SOURCE'] = ''
        libevfibers_cflags['MAP_ANONYMOUS'] = 'MAP_ANON'

    cflags = ''
    for k,v in libevfibers_cflags.items():
        op = '=' if v else ''
        cflags += ' -D' + k + op + v

    os.system('''
        cd %s &&
        cmake \
            -DCMAKE_C_FLAGS="%s" \
            -DCMAKE_EXE_LINKER_FLAGS="%s" \
            -DLIBEV_LIBRARY=%s \
            -DLIBEIO_LIBRARY=%s \
            -DLIBEV_INCLUDE_DIR=%s \
            -DLIBEIO_INCLUDE_DIR=%s \
            .. &&
        make
    ''' % ( libevfibers_dir_build, cflags, linker_flags,
            libev_sh_lib, libeio_sh_lib, libev_include, libeio_include,
        )
    )


Depends(libevfibers_ar_lib, [libev_ar_lib, libeio_ar_lib])


