add_languages('cpp')

cpp_compiler = meson.get_compiler('cpp')

gstreamer_dep = dependency('gstreamer-1.0')
gstreamer_base_dep = dependency('gstreamer-base-1.0')
gstreamer_plugins_base_dep = dependency('gstreamer-plugins-base-1.0')
appstream_dep = dependency('appstream', version: '>=0.12')
apt_pkg_dep = dependency('apt-pkg', version: '>=1.9.2')

# Check whether apt supports ddtp
ddtp_flag = []
if cpp_compiler.compiles(
  '''
    #include <apt-pkg/pkgcache.h>
    int main () {
      pkgCache::DescIterator d;
      return 0;
    }
  ''',
  dependencies: [
    apt_pkg_dep
  ]
)
  ddtp_flag = ['-DHAVE_DDTP']
endif

shared_module(
  'pk_backend_aptcc',
  'acqpkitstatus.cpp',
  'acqpkitstatus.h',
  'gst-matcher.cpp',
  'gst-matcher.h',
  'apt-messages.cpp',
  'apt-messages.h',
  'apt-utils.cpp',
  'apt-utils.h',
  'apt-sourceslist.cpp',
  'apt-sourceslist.h',
  'apt-cache-file.cpp',
  'apt-cache-file.h',
  'apt-intf.cpp',
  'apt-intf.h',
  'pkg-list.cpp',
  'pkg-list.h',
  'deb-file.cpp',
  'deb-file.h',
  'pk-backend-aptcc.cpp',
  include_directories: packagekit_src_include,
  dependencies: [
    packagekit_glib2_dep,
    gmodule_dep,
    apt_pkg_dep,
    gstreamer_dep,
    gstreamer_base_dep,
    gstreamer_plugins_base_dep,
    appstream_dep,
  ],
  cpp_args: [
    '-DG_LOG_DOMAIN="PackageKit-APTcc"',
    '-DPK_COMPILATION=1',
    '-DDATADIR="@0@"'.format(join_paths(get_option('prefix'), get_option('datadir'))),
    ddtp_flag,
  ],
  link_args: [
    '-lutil',
  ],
  override_options: [
    'c_std=c11',
    'cpp_std=c++17'
  ],
  install: true,
  install_dir: pk_plugin_dir,
)

install_data(
  '20packagekit',
  install_dir: join_paths(get_option('sysconfdir'), 'apt', 'apt.conf.d'),
)

install_data(
  'pkconffile.nodiff',
  install_dir: join_paths(get_option('datadir'), 'PackageKit', 'helpers', 'aptcc'),
)

if get_option('systemd')
  executable(
    'pk-debconf-helper',
    'pk-debconf-helper.c',
    dependencies: [
      packagekit_glib2_dep,
      libsystemd
    ],
    install: true,
    install_dir: get_option('libexecdir')
  )

  sd_config_data = configuration_data()
  sd_config_data.set('libexecdir', join_paths(get_option('prefix'), get_option('libexecdir')))

  configure_file(
    input: 'pk-debconf-helper.service.in',
    output: 'pk-debconf-helper.service',
    configuration: sd_config_data,
    install: true,
    install_dir: systemd_user_unit_dir,
  )
  configure_file(
    input: 'pk-debconf-helper.socket.in',
    output: 'pk-debconf-helper.socket',
    configuration: sd_config_data,
    install: true,
    install_dir: systemd_user_unit_dir,
  )
endif
