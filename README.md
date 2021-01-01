Liggghts input tutorial
=======================

**Input script structure**

This section describes the structure of a typical LIGGGHTS(R)-PUBLIC input script. Smilarly many sample input script can be found in example folder.

-------------------------------------------------------------------------------------------
A LIGGGHTS input script typically has 4 parts.

<!--ts-->
* [Initialization](#Initialization)
	* [Units](#Units)
	* [Dimension](#Dimension)
	* [Create_box](#Create_box)
	* [Newton](#Newton)
	* [Processor](#Processor)
	* [Boundary](#Boundary)
	* [atom_style](#atom_style)
	* [Atom_modify](#Atom_modify)
	* [Pair_style](#Pair_style)
	* [Bond_style](#Bond_style)
	* [Fix_wall_gran](#Fix_wall_gran)
	* [Fix_mesh_surface](#Fix_mesh_surface)
* [Atom_Particle definition and insertation](#Atom_Particle definition and insertation)	
	* [Read_data](#Read_data)
	* [Read_restart](#Read_restart)  	
	* [Region](#Region)
	* [Lattice](#Lattice)
	* [Create_atoms](#Create_atoms)
	* [Delete_atoms](#Delete_atoms)
	* [Groups](#Groups)
	* [Fix_insert_pack](#Fix_insert_pack)
	* [Fix_particletemplate_sphere](#Fix_particletemplate_sphere)

* [Settings](#Settings)
* [Run_simulation](#Run_simulation)
* [Training](#Training)
   
<!--te-->


-----------------------------------**Initialization**--------------------------------------------------------------------------------

Set parameters that need to be defined before atoms/particles are created or read in from a file.

The relevent commands are `units`, `dimension`, `newton`,`processors`, `boundary`, `atom_style`, `atom_modify`.

Units
======

style = lj or real or metal or si or cgs or electron or micro or nano

This command sets the style of units used for a simulation. it determines the units of all quantities specified in the input script and data files as well as quantities output to the screen, log file and dump files. 

`Typically, this commands is used at the very beginning of an input script`

**This command cannot be used after the simulation box is defined by a read_data or create_box command.**
----------------------------------------------------------------------------------------------

Dimension
===========

By default runs 3d.

To run a 2d simulation, this command should be used prior to setting up a simulation box via `create_box`, `read_data` commands.

Create_box
===========

```
create_box N region-ID

# N = # of atom types to use in this simulation
# region-ID of region to use as simulation domain(optional)
```

**An atom_style and one of region or simulation_domain must have been previously defined to use this command.**

Atom_style
===========

```
atom_style   ___

# style = bond or charge or ellipsoid or full or line or molecular or tri or hybrid or sphere or granular or bond/gran or superquadric or convexhull or sph
```


4. Region

```
region		___

#ID = user-assigned name for the region
#style = delete or block or cone or cylinder or plane or prism or sphere or mesh/tet or union or intersect or wedge

```



```
region 	1 block -3.0 5.0 INF 10.0 INF INF
region 	2 sphere 0.0 0.0 0.0 5 side out
region 	void cylinder y 2 3 5 -5.0 EDGE units box
region 	1 prism 0 10 0 10 0 10 2 0 0
region 	outside union 4 side1 side2 side3 side4
region 	2 sphere 0.0 0.0 0.0 5 side out move v_left v_up NULL
region 	1 wedge axis y  center 0 0  radius 10 bounds 0 10 angle0 -22.5 angle 45 units box side in
```



5. Lattice

 a lattice is simply a set of points in space, determined by a unit cell with basis atoms, that is replicated infinitely in all dimensions. The arguments of the lattice command can be used to define a wide variety of crystallographic lattices.


6. Create_atoms

This command creates atoms on a lattice, or a single atom, or a random collection of atoms, as an alternative to reading in their coordinates explicitly via a read_data or read_restart command.

Before using this command, a lattice must also be defined using the lattice command. The only exceptions are for the single style with units = box or the random style.



```
create_atoms 1 box
create_atoms 3 region regsphere basis 2 3
create_atoms 3 single 0 0 5
```




7. Delete_atoms

`style = group or region or overlap or porosity`

Delete the specified atoms. This command can be used to carve out voids from a block of material or to delete created atoms that are too close to each other (e.g. at a grain boundary).

```
delete_atoms group edge
delete_atoms region sphere compress no
delete_atoms overlap 0.3 all all
delete_atoms overlap 0.5 solvent colloid
delete_atoms porosity cube 0.1 123457
```



8. Groups 

Identify a collection of atoms as belonging to a group. The group ID can then be used in other commands such as fix, compute, dump, or velocity to act on those atoms together.

```
group edge region regstrip
group water type 3 4
group sub id 10 25 50
group sub id 10 25 50 500:1000
group sub id 100:10000:10
group sub id <= 150
group polyA molecule <> 50 250
group hienergy variable eng
group boundary subtract all a2 a3
group boundary union lower upper
group boundary intersect upper flow
group boundary delete
```


`All atoms belong to the “all” group.`



9. Newton

```
newton	off
newton	on off
```

10. Processor


Specify how processors are mapped as a 3d logical grid to the global simulation box. This involves 2 steps. First if there are P processors it means choosing a factorization P = Px by Py by Pz so that there are Px processors in the x dimension, and similarly for the y and z dimensions. Second, the P processors are mapped to the logical 3d grid. The arguments to this command control each of these 2 steps.

The Px, Py, Pz parameters affect the factorization. Any of the 3 parameters can be specified with an asterisk “*”, which means LIGGGHTS(R)-PUBLIC will choose the number of processors in that dimension of the grid. It will do this based on the size and shape of the global simulation box so as to minimize the surface-to-volume ratio of each processor’s sub-domain.



11. Boundary

```
boundary p p f
boundary p fs p
boundary s f fm
```

```
p is periodic
f is non-periodic and fixed
s is non-periodic and shrink-wrapped
m is non-periodic and shrink-wrapped with a minimum value
```



12. Atom_modify

```
atom_modify map hash
atom_modify map array sort 10000 2.0
atom_modify first colloid
```

The map keyword determines how atom ID lookup is done for molecular problems. Lookups are performed by bond (angle, etc) routines in LIGGGHTS(R)-PUBLIC to find the local atom index associated with a global atom ID. When the array value is used, each processor stores a lookup table of length N, where N is the total # of atoms in the system. This is the fastest method for most simulations, but a processor can run out of memory to store the table for very large simulations. The hash value uses a hash table to perform the lookups. This method can be slightly slower than the array method, but its memory cost is proportional to N/P on each processor, where P is the total number of processors running the simulation.


13. Pair_style

14. Bond_style

15. fix wall/gran


-------------------------------------------------------**Atom/particle definitaion and insertation**----------------------------------


10. Read_data

11. Read_restart

12. Fix/insert/pack





```
# an example of initialization

### Initialization

units		si
atom_style	sphere
atom_modify	map array
boundary	f f f
newton		off
communicate	single vel yes
#processors	2 2 3

# Declare domain
region		domain block -0.138 0.138 -0.138 0.138 -0.0045 0.43 units box
create_box	2 domain


# Neighbor listing
neighbor	0.003 bin
neigh_modify	every 1 check no
```


		







