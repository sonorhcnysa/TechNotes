## uuidgen
`function uuidgen { [Guid]::NewGuid().ToString() }`

## grep
use `select-string`:
- `get-content $file | select-string "sought string"`
- `select-string -Path d:\app\data\* -Include *.log "sought string"`
- `select-string -Path $file "sought string"`
- `select-string -Path $file -Encoding unicode "sought string"`
- `> select-string -path D:\docs\Linode\LinodeServerHowTo.txt -pattern "/bin/.*sh"`
  `D:\docs\Linode\LinodeServerHowTo.txt:94:     (sudo /bin/bash)`
  `D:\docs\Linode\LinodeServerHowTo.txt:115:#! /bin/bash`
