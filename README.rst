Template for the Read the Docs tutorial
=======================================

This GitHub template includes fictional Python library
with some basic Sphinx docs.

Read the tutorial here:

https://docs.readthedocs.io/en/stable/tutorial/

How to run
==========

1. **Install Python and Virtualenv**

   Ensure you have Python installed on your system. Install ``virtualenv`` if you haven't already::

     pip install virtualenv

2. **Create a Virtual Environment**

   Navigate to your project's root directory and create a virtual environment::

     cd /path/to/your/project
     virtualenv venv

3. **Activate the Virtual Environment**

   Activate the virtual environment. The command differs based on your operating system:

   - **Windows**::

     venv\Scripts\activate

   - **MacOS/Linux**::

     source venv/bin/activate

4. **Install Sphinx and Dependencies**

   Install Sphinx and any other dependencies specified in your ``requirements.txt`` or ``docs/requirements.txt`` file::

     pip install sphinx
     pip install -r docs/requirements.txt

5. **Build the Documentation**

   Navigate to the ``docs`` directory and build the HTML documentation::

     cd docs
     make html
6. **Development Mode**

   In dev mode run the following to enable automatic updates::

     sphinx-autobuild ./source/ _build/html 

7. **View the Documentation**

   Open the generated HTML files in your browser. The main file is usually located at ``_build/html/index.html``.