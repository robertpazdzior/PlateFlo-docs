Perfusion Event Scheduler
##########################

Event scheduler, monitor, and executer.

1. Key Features
^^^^^^^^^^^^^^^
* Simplifies the creation of complex perfusion programs
* Create 'events' - tasks to be carried out at a specific time
* Recurring events - schedule events that repeat at arbitrary intervals
* Monitors scheduled events and executes tasks automatically
* Tasks can be any function - positional and keyword arguments are passed

2. Quick Start
^^^^^^^^^^^^^^^

Event execution times in :mod:`.scheduler` are defined using ``datetime``
objects, therefore require the Python `datetime module
<https://docs.python.org/3/library/datetime.html>`_ to create. ``timedelta`` is
also used when defining relative times.

.. code-block:: python

    >>> from plateflo import scheduler
    >>> from datetime import datetime, timedelta

    >>> # instantiate the scheduler object
    >>> sched = scheduler.Scheduler()

Create a one-time event, :class:`.SingleEvent()`, scheduled for 5s after the
script starts, and add it to the :class:`.Scheduler()`:

.. code-block:: python

    >>> # define the time
    >>> event_time = datetime.now() + timedelta(seconds=5)

    >>> # create the event
    >>> dinger_event = scheduler.SingleEvent(event_time, print, args=['Ding!'])
 
    >>> # add the event to the scheduler
    >>> sched.add_event(dinger_event)

Call :meth:`.monitor()` in the main loop of your script to execute the scheduled
event at the defined time.

.. code-block:: python

    >>> while sched.events:
    >>>     sched.monitor()
    >>> # five seconds later...
    Ding!

.. tip::

    Repeatedly calling :meth:`.monitor()` (or anything else, for that matter) in
    the main loop will needlessly consume CPU cycles. Depending on what else
    your main loop is doing and the granularity of your event scheduling, adding
    a simple ``time.sleep(0.05)`` 50ms delay can dramatically reduce CPU usage.

3. Usage
^^^^^^^^^

i. Functional Overview
======================

Events
-------

The functional core of the :mod:`.scheduler` module is its ``Event`` objects.
Events contain all of the information required to schedule, execute, and
reschedule ``tasks`` - function calls.

There are two classes of ``Event`` object:

:One-Time Events: :class:`.SingleEvent()`
:Recurring Events: :class:`.RecurringEvent()`, :func:`.DailyEvent()`

As their names imply, a :class:`.SingleEvent()` is executed at a specific time,
a ``datetime`` object, while a :class:`.RecurringEvent()` executes repeatedly at
a given interval, a ``timedelta`` object.

Both events execute the supplied ``task`` when due - a callable object. Both
positional and keyword arguments are passed to the ``task``\'s function pointer
on execution.

Scheduling
-----------

The :class:`.Scheduler()` keeps track of all ``Events`` in an :attr:`.events`
list, each with its own ``eventID``, generated and returned when adding events
to the scheduler.

``Event`` objects are added :class:`.Scheduler()` with the :meth:`.add_event()`
method, and removed using the :meth:`.remove_event()` method. Events are 
automatically sorted in the :attr:`.events` list based on their scheduled
execution time, therefore the :attr:`.events` list can be considered a queue.

The :meth:`.monitor()` method compares the current system time with that of the
first ``Event`` in the queue and calls the ``task`` function when due. The 
``Event`` is then popped from the queue and transferred to the
:attr:`.event_history` list.

ii. Creating Event Objects
==========================

One-Time Events
---------------

Tasks that need only be executed once are created using :class:`.SingleEvent()`
objects.

The event is executed at the absolute time specified by a ``datetime`` object.
See the section below on defining event tasks.

.. autoclass:: plateflo.scheduler.SingleEvent
    :noindex:

Recurring Events
----------------
    
Events that take place at regular intervals are created using
:class:`.RecurringEvent()` objects, which can take several parameters to fine
tune define their behaviour:

.. autoclass:: plateflo.scheduler.RecurringEvent
    :noindex:

To simplify the definition of daily events that take place at a specific
clock time, the :class:`.DailyEvent()` class can be used.

.. autoclass:: plateflo.scheduler.DailyEvent
    :noindex:

Passing Functions to Event Objects
----------------------------------

Tasks are passed into ``Events`` passed in as callable objects, basically a
function's name without the round brackets that usually follow. Positional
arguments, ``args``, are passed in as a list and keyword arguments passed after
all other arguments.

For example, to toggle a valve using a :doc:`FETbox</software/fetbox>` using
:meth:`.hit_hold_chan()` after 3 seconds and then :meth:`.disable_chan()` 3
seconds after that.

.. code-block:: python

    >>> # FETbox object
    >>> fet = auto_connect_fetbox()[0]

    >>> now = datetime.now()

    >>> # Using positional arguments
    >>> valve_on = scheduler.SingleEvent(dateTime = now + timedelta(seconds=3), 
    >>>                                  task = fet.hit_hold_chan,
    >>>                                  args=[1, 0.8])

    >>> # Using a keyword argument
    >>> valve_off = scheduler.SingleEvent(dateTime = now + timedelta(seconds=6), 
    >>>                                   task = fet.disable_chan,
    >>>                                   chan = 1)

A ``lambda function`` can be used to define a callable object whose arguments
are undetermined until the time of execution.

.. code-block:: python

    >>> print_time = lambda : print(datetime.now())
    >>> ticker = scheduler.RecurringEvent(interval = timedelta(seconds=1),
    >>>                                   task = print_time)

iii. Adding/Removing Events
===========================

``Events`` are added to the :class:`.Scheduler()` using the :meth:`.add_event()`
method, which returns the unique serialized ``eventID`` for that event.

.. code-block:: python

    >>> my_event # any Event type
    >>> my_event_id = sched.add_event(my_event)
    >>> print(my_event_id)
    1

The ``eventID`` can be used to remove the event from the scheduler e.g. to
cancel a :class:`.RecurringEvent()`:

.. code-block:: python

    >>> print(sched.events)
    [{'eventID': 1, 
      'dateTime': datetime.datetime(2021, 2, 11, 16, 22, 37, 196192), 
      'event': <plateflo.scheduler.RecurringEvent object at 0x000001F2946FBA90>}]
    >>> sched.remove_event(my_event_id)
    >>> print(sched.events)
    []

4. Working Examples
^^^^^^^^^^^^^^^^^^^

Toggle a `FETbox`-attached valve at regular intervals for a few cycles:

.. code-block:: python
    :linenos:

    >>> from plateflo import scheduler, fetbox, serial_io
    >>> from datetime import datetime, date, timedelta
    >>> from time import sleep
    >>> import logging
    
    >>> # enable logging to see info in the terminal
    >>> logging.basicConfig(level=logging.INFO)

    >>> sched = scheduler.Scheduler() 
    >>> # auto-connect to FETbox w/ ID 0
    >>> fet = fetbox.auto_connect_fetbox()[0]

    >>> # Create recurring events

    >>> INTERVAL = 1 # on/off interval
    >>> CYCLES = 8   # total on/off cycles
    
    >>> interval = timedelta(seconds = INTERVAL*2)
    >>> stop_time = datetime.now() + timedelta(seconds = INTERVAL*2*CYCLES)

    >>> valve_on_evt = scheduler.RecurringEvent(interval = interval, 
    >>>                                         stop_time = stop_time,
    >>>                                         task = fet.enable_chan,
    >>>                                         chan = 1) # this is a task keyword argument
    >>> valve_off_evt = scheduler.RecurringEvent(interval = interval,
    >>>                                          stop_time = stop_time,
    >>>                                          task = fet.disable_chan,
    >>>                                          delay = timedelta(seconds=1),
    >>>                                          chan = 1) # this is a task keyword argument
    
    >>> sched.add_event(valve_on_evt)
    >>> sched.add_event(valve_off_evt)
     
    >>> def main():
    >>>     while sched.events:
    >>>         sched.monitor()
    >>>         sleep(1E-6)
    >>>     fet.kill()
     
    >>> if __name__ == "__main__":
    >>>     main()

.. code-block::
    
    >>> # program output:
    INFO:FETbox:Scanning for connected FETbox(es)...
    INFO:FETbox:Scanning COM6...
    INFO:FETbox:            FETbox (ID 0) detected.
    INFO:FETbox:COM6 FETbox (ID: 0) initialized
    INFO:FETbox:COM6 Enabled chan. 1
    INFO:FETbox:COM6 Disabled chan. 1
    INFO:FETbox:COM6 Enabled chan. 1
    INFO:FETbox:COM6 Disabled chan. 1
    INFO:FETbox:COM6 Enabled chan. 1
    INFO:FETbox:COM6 Disabled chan. 1
    INFO:FETbox:COM6 FETbox CLOSED