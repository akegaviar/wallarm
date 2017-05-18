.. code-block:: ini

    #!/bin/sh
    set -e

    if [ -z "$PROTONDB_UPDATED" -a -z "$LOM_UPDATED" ]; then
            exit 42
    fi

    your/command/to/restart/nginx