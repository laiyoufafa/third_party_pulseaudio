libpulsecore_sources = [
  'asyncmsgq.c',
  'asyncq.c',
  'auth-cookie.c',
  'card.c',
  'cli-command.c',
  'cli-text.c',
  'client.c',
  'core-scache.c',
  'core-subscribe.c',
  'core.c',
  'cpu.c',
  'cpu-arm.c',
  'cpu-orc.c',
  'cpu-x86.c',
  'device-port.c',
  'ffmpeg/resample2.c',
  'filter/biquad.c',
  'filter/crossover.c',
  'filter/lfe-filter.c',
  'hook-list.c',
  'ltdl-helper.c',
  'mix.c',
  'modargs.c',
  'modinfo.c',
  'module.c',
  'msgobject.c',
  'namereg.c',
  'object.c',
  'play-memblockq.c',
  'play-memchunk.c',
  'remap.c',
  'resampler.c',
  'resampler/ffmpeg.c',
  'resampler/peaks.c',
  'resampler/trivial.c',
  'rtpoll.c',
  'sconv-s16be.c',
  'sconv-s16le.c',
  'sconv.c',
  'shared.c',
  'sink.c',
  'sink-input.c',
  'sioman.c',
  'sound-file-stream.c',
  'sound-file.c',
  'source.c',
  'source-output.c',
  'start-child.c',
  'stream-util.c',
  'thread-mq.c',
]

libpulsecore_headers = [
  'asyncmsgq.h',
  'asyncq.h',
  'auth-cookie.h',
  'card.h',
  'cli-command.h',
  'cli-text.h',
  'client.h',
  'core.h',
  'core-scache.h',
  'core-subscribe.h',
  'cpu.h',
  'cpu-arm.h',
  'cpu-orc.h',
  'cpu-x86.h',
  'database.h',
  'device-port.h',
  'ffmpeg/avcodec.h',
  'ffmpeg/dsputil.h',
  'filter/biquad.h',
  'filter/crossover.h',
  'filter/lfe-filter.h',
  'hook-list.h',
  'ltdl-helper.h',
  'mix.h',
  'modargs.h',
  'modinfo.h',
  'module.h',
  'msgobject.h',
  'namereg.h',
  'object.h',
  'play-memblockq.h',
  'play-memchunk.h',
  'remap.h',
  'resampler.h',
  'rtpoll.h',
  'sconv.h',
  'sconv-s16be.h',
  'sconv-s16le.h',
  'shared.h',
  'sink-input.h',
  'sink.h',
  'sioman.h',
  'sound-file-stream.h',
  'sound-file.h',
  'source-output.h',
  'source.h',
  'start-child.h',
  'stream-util.h',
  'thread-mq.h',
  'typedefs.h',
]

if get_option('database') == 'tdb'
  libpulsecore_sources += 'database-tdb.c'
  database_c_args = '-DHAVE_TDB'
elif get_option('database') == 'gdbm'
  libpulsecore_sources += 'database-gdbm.c'
  database_c_args = '-DHAVE_GDBM'
else
  libpulsecore_sources += 'database-simple.c'
  database_c_args = '-DHAVE_SIMPLEDB'
endif

if dbus_dep.found()
  libpulsecore_sources += [
    'dbus-shared.c',
    'protocol-dbus.c',
  ]
  libpulsecore_headers += [
    'dbus-shared.h',
    'protocol-dbus.h',
  ]
endif

# FIXME: walk through dependencies and add files

# FIXME: SIMD support (ORC)
simd = import('unstable-simd')
libpulsecore_simd = simd.check('libpulsecore_simd',
  mmx : ['remap_mmx.c', 'svolume_mmx.c'],
  sse : ['remap_sse.c', 'sconv_sse.c', 'svolume_sse.c'],
  neon : ['remap_neon.c', 'sconv_neon.c', 'svolume_neon.c'],
  c_args : [pa_c_args],
  include_directories : [configinc, topinc],
  implicit_include_directories : false,
  compiler : cc)
libpulsecore_simd_lib = libpulsecore_simd[0]
cdata.merge_from(libpulsecore_simd[1])

# FIXME: Implement Windows support
#'mutex-win32.c',
#'poll-win32.c',
#'semaphore-win32.c',
#'thread-win32.c',

libpulsecore = shared_library('pulsecore-' + pa_version_major_minor,
  libpulsecore_sources,
  libpulsecore_headers,
  include_directories : [configinc, topinc],
  c_args : [pa_c_args, server_c_args],
  install : true,
  link_with : libpulsecore_simd_lib,
  dependencies : [libm_dep, libpulsecommon_dep, libpulse_dep, ltdl_dep, shm_dep, sndfile_dep, database_dep, dbus_dep, x11_dep],
  implicit_include_directories : false)

libpulsecore_dep = declare_dependency(link_with: libpulsecore)

# For modules that need protocol native functionality
libprotocol_native = shared_library('protocol_native',
  'protocol-native.c',
  ['protocol-native.h', 'native-common.h'],
  c_args : [pa_c_args, server_c_args, database_c_args],
  include_directories : [configinc, topinc],
  dependencies : [libpulse_dep, libpulsecommon_dep, libpulsecore_dep, dbus_dep],
  install : true
)