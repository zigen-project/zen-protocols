project(
  'zen-protocols', 'c',
  version: '0.0.1',
  license: 'MIT',
  meson_version: '>= 0.58.0',
  default_options: [ 'warning_level=3', 'werror=true', 'optimization=2', 'c_std=gnu11' ],
)

wayland_protocols_req = '>= 1.24'
wayland_req = '>= 1.18.0'

wayland_scanner_dep = dependency('wayland-scanner')
wayland_protocols_dep = dependency('wayland-protocols', version: wayland_protocols_req)
wayland_server_dep = dependency('wayland-server', version: wayland_req)
zigen_protocols_dep = dependency('zigen-protocols')

pkgconfig = import('pkgconfig')

prog_scanner = find_program(wayland_scanner_dep.get_variable('wayland_scanner'))
zigen_protocols_dir = zigen_protocols_dep.get_variable('pkgdatadir')

protocols = {
  'unstable': {
    'zen-space': {}
  },
  zigen_protocols_dir: {
    'zigen': {},
  }
}

# structure of `protocols`
#
# protocols = {
#   directory: {
#     name: {
#       'path': <abs path to source xml>,
#       'client-header': <target>,
#       'server-header': <target>,
#       'private-code' : <target>,
#     },
#   },
# }

foreach directory, protos : protocols
  foreach name, info : protos
    file = files(join_paths(directory, '@0@.xml'.format(name)))

    info += {
      'path': join_paths(meson.current_source_dir(), directory, '@0@.xml'.format(name))
    }

    foreach  output_type : [ 'client-header', 'server-header', 'private-code' ]
      if output_type == 'client-header'
        output_file = '@0@-client-protocol.h'.format(name)
      elif output_type == 'server-header'
        output_file = '@0@-server-protocol.h'.format(name)
      else
        output_file = '@0@-protocol.c'.format(name)
      endif

      target = custom_target(
        '@0@ @1@'.format(name, output_type),
        command: [ prog_scanner, output_type, '@INPUT@', '@OUTPUT@', '-s' ],
        input: file,
        output: output_file,
      )

      info += {output_type: target}

    endforeach

    protos += {name: info}

    if name in [ 'zen-space' ]
      install_data(file)
    endif

  endforeach 

  protocols += {directory: protos}
endforeach

subdir('tests')

pkgconfig.generate(
  name: 'ZEN Protocols',
  description: 'ZEN protocol files',
  version: meson.project_version(),
  variables: [
    'datarootdir=' + join_paths('${prefix}', get_option('datadir')),
    'pkgdatadir=' + join_paths('${datarootdir}', meson.project_name()),
  ],
  filebase: 'zen-protocols',
)
