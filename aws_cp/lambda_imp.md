If the second invocation uses the same warm execution environment,  /tmp/model.bin  can be reused, so the model does not need to be downloaded again. Your code should check whether the file exists before downloading it.

import os

model_path = "/tmp/model.bin"

if not os.path.exists(model_path):
    download_model(model_path)

use_model(model_path)

However,  /tmp  is only temporary and reuse is not guaranteed:

• A new execution environment starts with an empty  /tmp .
• AWS may delete the old environment at any time.
• Different concurrent environments each have their own separate  /tmp .
•  /tmp  contents can remain across warm invocations but should not be treated as permanent storage.
