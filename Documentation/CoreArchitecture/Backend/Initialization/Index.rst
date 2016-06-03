
.. include:: ../../../Includes.txt


.. _initialization:

Initialization
^^^^^^^^^^^^^^

Whenever a call to TYPO3 CMS is made, the application goes through a
bootstrapping process managed by a dedicated API. This process is also
used in the frontend, but only the backend process is described here.

The bootstrapping class is :code:`\TYPO3\CMS\Core\Core\Bootstrap`.
It goes through the steps described below.

Define legacy constants
  In this step some constants are defined, which will eventually
  be dropped, but are still initialized for now.

Initialize class loader
  This defines which autoloader to use.

Perform base setup
  An instance of :code:`\TYPO3\CMS\Core\Core\SystemEnvironmentBuilder` is
  created. This class in turn defines a large number constants and global
  variables. If you want to have an overview of these base values, it is
  worth taking a look into the following methods:

  - :code:`SystemEnvironmentBuilder::defineBaseConstants()` defines
    constants containing values such as the current version number,
    blank character codes and error codes related to services.

  - :code:`SystemEnvironmentBuilder::definePaths()` defines constants
    containing paths to various parts of the TYPO3 installation like
    the absolute path to the :file:`typo3` directory or the absolute
    path to the installation root.

  - :code:`SystemEnvironmentBuilder::checkMainPathsExist()` checks if
    expected paths like :file:`typo3` or :file:`index.php` exist. If that
    is not the case, the process will quit immediatly.

  - :code:`SystemEnvironmentBuilder::initializeGlobalVariables()` sets
    some global variables as empty arrays.

  - :code:`SystemEnvironmentBuilder::initializeGlobalTimeTrackingVariables()`
    defines special variables which contain, for example, the current time or
    a simulated time as may be set using the Admin Panel.

Define class loading information
  This part of the process all the information available to be able to
  determine where to load classes from, including class alias maps which
  are used to map legacy class names to new class names.

Check essential configuration
  In this step we check if crucial configuration elements have been set.
  If that is not the case, the installation is deemed incomplete and the
  user is redirected to the Install Tool.

Register request handlers
  The backend recognizes three request handlers, one to handle general requests,
  one for backend module requests and one for AJAX requests.

More configuration
  Next :code:`Bootstrap::configure()` is called which in turn triggers
  a whole new series of configuration. This is actually a major step,
  with too many actions to detail efficiently here. However here is the
  list of the most important stuff happening at this point:

  - the main configuration ("TYPO3_CONF_VARS") is loaded

  - the Caching Framework and the Package Management are set up

  - all configuration items from extensions are loaded

  - the database connections is established

After all that the :code:`Bootstrap::run()` method is called, which
basically dispatches the request to the right handler.

The request handler has its own :code:`boot()` method, which performs
yet more initialization and set up as needed. A general request to the
backend will typically go through such important steps like:

- checking backend access (is it locked? does it have proper SSL setup?)

- loading the full :ref:`TCA <t3tca:start>`

- verifying and initializing the backend user

