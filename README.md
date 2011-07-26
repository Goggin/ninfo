nInfo
=====

nInfo is a tool and framework (and lots of plugins) for gathering information on an

 * IP Address
 * Hostname
 * Username

It consists of multiple plugin classes that implement a `get_info` function.
The classes contain metadata for the type of arguments they accept, and if
they are relevant for internal and or external hosts.

The tool
--------

Listing plugins
~~~~~~~~~~~~~~~

    $ ninfo -l
    Name                 Title                Description
    cif                  CIF                  Collective Intelligence Framework
    cymruwhois           Cymru Whois          Cymru Whois lookup
    geoip                GeoIP                GeoIP
    google_safebrowsing  Google Safe Browsing Google Safe Browsing check
    ....

Getting information
~~~~~~~~~~~~~~~~~~~
Silly example, run two plugins against two addreses:

    $ ninfo -p geoip -p cymruwhois 8.8.8.8 4.2.2.2                                           2011-07-26 10:36:14 justin pts/15
    === 8.8.8.8 === 
    *** Cymru Whois (Cymru Whois lookup) ***
    15169 US 8.8.8.0/24 GOOGLE - Google Inc.

    *** GeoIP (GeoIP) ***
    US - United States

    === 4.2.2.2 === 
    *** Cymru Whois (Cymru Whois lookup) ***
    3356 US 4.0.0.0/9 LEVEL3 Level 3 Communications

    *** GeoIP (GeoIP) ***
    US - United States


Writing A plugin
----------------

Here's a plugin:

    from ninfo import PluginBase

    class fun_plugin(PluginBase):
        """This plugin returns something cool!"""

        name        =  'fun'
        title       =  'Fun Plugin'
        description =  'Happy Fun time'
        cache_timeout   =  60*2
        types   =    ['ip','hostname']

        #def setup(self):
        #    #libraries should be lazy imported in setup. This is only called once.
        #    import mybackendlibrary
        #    self.client = mybackendlibrary.Client()

        def get_info(self, arg):
            #should always return a dictionary, even for a single value
            #multiple values are the norm, and allow values to be added without breakage
            result = 'hello %s' % arg
            return { "result": result }

    plugin_class = fun_plugin

If installed, this plugin can be ran as follows:

    >>> from ninfo import Ninfo
    >>> p = Ninfo()
    >>> print p.get_info('fun', 'justin.rules')
    {'result': 'hello justin.rules'}

I had to include a '.' in the argument, because without it, ninfo will assume
the argument is a 'user' and not an 'ip' or a 'hostname', and it will not run
the plugin.

Plugins are installed and located using entry_points. If the above class was in a python module
called fun_plugin, it would be installed by the following in setup.py:

    ...
    py_modules = [ "fun_plugin"],
    entry_points = {
        'ninfo.plugin': [
            'fun = fun_plugin',
        ]
    ...

See Also
--------

* Plugins - https://github.com/JustinAzoff/ninfo_plugins/
* Web Interface - https://github.com/JustinAzoff/ninfo_web/
