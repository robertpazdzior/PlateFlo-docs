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

>>> from plateflo import scheduler
>>> from datetime import datetime, timedelta
>>> 
>>> # instantiate the scheduler object
>>> sched = scheduler.Scheduler()

Create a one-time event, :class:`.SingleEvent()`, scheduled for 5s after the
script starts:

>>> event_time = datetime.now() + timedelta(seconds=5)
>>>
>>> dinger_event = scheduler.SingleEvent(event_time, print, args=['Ding!'])
>>> sched.add_event(dinger_event)

Call :meth:`.monitor()` in the main loop of your script to execute the scheduled
event at the defined time.

>>> while sched.events:
>>>     sched.monitor()

Five seconds later...
    >>> Ding!

.. tip::

    Repeatedly calling :meth:`.monitor()` (or anything else, for that matter) in
    the main loop will needlessly consume CPU cycles. Depending on what else
    your main loop is doing and the granularity of your event scheduling, adding
    a simple ``time.sleep(0.05)`` 50ms delay can dramatically reduce CPU usage.

3. Usage
^^^^^^^^^

Overview
=========

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

Both events execute the supplied ``task`` when due - a function pointer. Both
positional and keyword arguments are passed to the ``task``\'s function pointer on
execution.

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




4. Examples
^^^^^^^^^^^^