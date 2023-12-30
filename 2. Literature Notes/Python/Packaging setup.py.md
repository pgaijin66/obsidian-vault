1. Write code
2. Create `setup.py or setup.cfg or pyproject.toml`
3. Add followings 
```
import os
 
from distutils.core import setup

setup(
    name = 'PACKAGE NAME',
    version = '0.1.0',
    description = 'PROJECT DESCRIPTION',
    keywords = 'KEYSWORDS SEPERATED BY SPACE',
    license = 'GNU LESSER GENERAL PUBLIC LICENSE',
    author = 'AUTHOR NAME',
    author_email = 'EMAIUL',
    maintainer = 'MAINTAINER NAME',
    maintainer_email = 'MAINTAINER EMAIL',
    url = 'PROJECT URL',
    dependency_links = [],
    classifiers=[
        'Development Status :: 3 - Alpha',
        'Environment :: Plugins',
        'Framework :: Django',
        'Intended Audience :: Developers',
        'License :: OSI Approved :: BSD License',
        'Programming Language :: Python',
        'Topic :: Software Development :: Libraries :: Python Modules',
    ],
    packages = packages,
    include_package_data = True,
)
```
   4. Build package `python3 setup.py dest`
   5. Test it out. 
	   1. Create a `venv` `python3 -m venv venv`
	   2. Activate the `venv` : `source venv/bin/activate`
	   3. Install package from the dest dir `pip3 install dest/hello-world-1.0.0.tar.gz`
	   4. Import function `from hell_world import say_hello`
	   5. `say_hello()`