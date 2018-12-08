Chapter 4. Commands to Edit Data
=======================================

In this chapter, I will introduce ``DataCmd`` and its subclasses used to
modify ``Data`` and its subclasses.

There are a lot of command objects introduced, and they can be classified
as the following table.

.. csv-table::
	:widths: 5, 30

	"Add", "``List::append``, ``List::insert``, ``Set::add``,
	``Map::add``, ``Single::setValid``"
	"Remove", "``List::remove``, ``List::clear``, ``Set::remove``,
	``Set::clear``, ``Map::remove``, ``Map::clear``, ``Single::setValid``,
	``Single::setNull``"
	"Move", "``List::move``"

Command for ownership and command for reference
are provided for each of methods enumerated above.

.. csv-table::
	:header: "", "For Ownership", "For Reference"
	:widths: 10, 30, 30

	Add", "Command create an ``Data`` instance and add to the container.
	Since it actually validates and invalidates the instance, it runs
	``solution()`` as well.", "Simply add an instance to the container."
	Remove", "Command invalidates an instance and remove from the container.
	``solution()`` is run as well.", "Simply remove an existing reference."
	Move", "Command simply move position of an instance in the same
	container. No ``Data::solution()`` is invoked.", "Same with Ownership."

This is common traits of each classified command types.

You need to open the source code committed with message
``004. Commands to edit Data`` before read the extra chapter.

Add-Ownership Commands
----------------------------

Open ``list_container_cmd.h``.

Let's see ``CreateAppendToList`` class template. Following the traits listed
above, it actually creates a new ``Data`` instance and uses
``Data::solution()`` in ``undo()`` function, to solve the instance's dependency.
This is because ``undo()`` is opposite of creating new instance, which is
deleting, which let the instance invalidated. Then it should not be referred
by anywhere else, and it's the reason why ``solution()`` is saved and called.

Attention that the class only creates the ``Data`` once, even though ``undo()``
is called after ``run()`` is called. This prevents the instance has different
allocation position even though the command is once undone and redone.

Remove-Ownership Commands
------------------------------

``RemoveFromList`` class template is an example of Remove-ownership command.
Running it calls ``Data::solution()->run()`` so that every reference can be
disconnected before it's actually removed(invalidated). The solution is
recovered(undone) when ``undo()`` is invoked, after the object is validated.

Move Commands
--------------------

There's no difference between Move-ownership and Move-reference, since in
both cases no parent-child relationship breaking is occurred.

The class simply takes 2 indexes, *moveThis* and *toHere*, and call
``ListContainer::move(moveThis, toHere)``. Undoing swaps the parameters, into
``move(toHere, moveThis)``.

Add-Reference and Remove-Reference Commands
------------------------------------------------------

They are really simple just like Move commands. They take *container* and
*data* instance, and add or remove from the container depends which
one of ``run()`` or ``undo()`` is called.
