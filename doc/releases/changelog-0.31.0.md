:orphan:

# Release 0.31.0 (current release)

<h3>New features since last release</h3>

* Added a new function `qml.ops.functions.bind_new_parameters` that creates a copy of an operator with new parameters without mutating the original operator.
  [(#4113)](https://github.com/PennyLaneAI/pennylane/pull/4113)
  [(#4256)](https://github.com/PennyLaneAI/pennylane/pull/4256)

* Added the `TRX` qutrit rotation operation, which allows applying an X rotation on a
  given subspace.
  [(#2845)](https://github.com/PennyLaneAI/pennylane/pull/2845)

* Added the `TRY` qutrit rotation operation, which allows applying a Y rotation on a
  given subspace.
  [(#2846)](https://github.com/PennyLaneAI/pennylane/pull/2846)

* Added the `TRZ` qutrit rotation operation, which allows applying a Z rotation on a
  given subspace.
  [(#2847)](https://github.com/PennyLaneAI/pennylane/pull/2847)

* A function `pauli.pauli_word_prefactor()` is added to extract the prefactor for a given Pauli word.
  [(#4164)](https://github.com/PennyLaneAI/pennylane/pull/4164)

* Added the `FermiWord` class to represent a fermionic operator such as
  :math:`a^\dagger_1 a_0 a^\dagger_2 a_3`.
  [(#4191)](https://github.com/PennyLaneAI/pennylane/pull/4191)

* Added a conversion function `jordan_wigner` that converts a fermionic operator (`FermiWord`) to a qubit 
  `Operator` (or its equivalent `PauliSentence` based on an optional kwarg) using the Jordan-Wigner mapping. 
  It also includes the behaviour of the existing `qchem.jordan_wigner` function, and replaces it.
  [(#4201)](https://github.com/PennyLaneAI/pennylane/pull/4201)
  [(#4253)](https://github.com/PennyLaneAI/pennylane/pull/4253)

* Added the `FermiSentence` class to represent a linear combination of fermionic operators.
  [(#4195)](https://github.com/PennyLaneAI/pennylane/pull/4195)

* Added `FermiC` and `FermiA` classes as representations of the fermionic creation and annihilation 
  operators. These user-facing classes for creating fermonic operators are accessible as, e.g.,
  `qml.FermiC(0)` and `qml.FermiA(3)`.
  [(#4200)](https://github.com/PennyLaneAI/pennylane/pull/4200)

* Added the function `string_to_fermi_word` to create a `FermiWord` object from a compact string
  representation.
  [(#4229)](https://github.com/PennyLaneAI/pennylane/pull/4229)

* Added new string representation to `FermiWord` and `FermiSentence`.
  [(#4255)](https://github.com/PennyLaneAI/pennylane/pull/4255)

* Added the `QutritBasisState` operator to support qutrit state preparation for the `default.qutrit` device
  [(#4185)](https://github.com/PennyLaneAI/pennylane/pull/4185)

* Added dunder methods to `FermiWord` and `FermiSentence` to allow arithmetic operations 
  using `+`, `-` and `*` between 
  `FermiWord`, `FermiSentence` and `int`, `float` and `complex` objects.
  [(#4209)](https://github.com/PennyLaneAI/pennylane/pull/4209)
  [(#4262)](https://github.com/PennyLaneAI/pennylane/pull/4262)

* Added the `one_qubit_decomposition` function to provide a unified interface for all one qubit decompositions. All
  decompositions simplify the rotations angles to be between `0` and `4` pi.
  [(#4210)](https://github.com/PennyLaneAI/pennylane/pull/4210)
  [(#4246)](https://github.com/PennyLaneAI/pennylane/pull/4246)

<h3>Improvements 🛠</h3>

* The experimental device interface is integrated with the `QNode`.
  [(#4196)](https://github.com/PennyLaneAI/pennylane/pull/4196)

* `Projector` now accepts a state vector representation, which enables the creation of projectors
  in any basis.
  [(#4192)](https://github.com/PennyLaneAI/pennylane/pull/4192)

  ```python
  dev = qml.device("default.qubit", wires=2)
  @qml.qnode(dev)
  def circuit(state):
      return qml.expval(qml.Projector(state, wires=[0, 1]))
  zero_state = [0, 0]
  plusplus_state = np.array([1, 1, 1, 1]) / 2
  ```
  ```pycon
  >>> circuit(zero_state)
  1.
  >>> 
  >>> circuit(plusplus_state)
  0.25
  ```

* The pulse differentiation methods, `pulse_generator` and `stoch_pulse_grad` now raise an error when they
  are applied to a `QNode` directly. Instead, use differentiation via a JAX entry point (`jax.grad`, `jax.jacobian`, ...).
  [(4241)](https://github.com/PennyLaneAI/pennylane/pull/4241)

* `pulse.ParametrizedEvolution` now raises an error if the number of input parameters does not match the number
  of parametrized coefficients in the `ParametrizedHamiltonian` that generates it. An exception is made for
  `HardwareHamiltonian`s which are not checked.
  [(4216)](https://github.com/PennyLaneAI/pennylane/pull/4216)
  
* The stochastic parameter-shift gradient transform for pulses, `stoch_pulse_grad`, now
  supports arbitrary Hermitian generating terms in pulse Hamiltonians.
  [(4132)](https://github.com/PennyLaneAI/pennylane/pull/4132)

* `DiagonalQubitUnitary` now decomposes into `RZ`, `IsingZZ` and `MultiRZ` gates
  instead of a `QubitUnitary` operation with a dense matrix.
  [(#4035)](https://github.com/PennyLaneAI/pennylane/pull/4035)
  
* The Jax-JIT interface now uses symbolic zeros to determine trainable parameters.
  [(4075)](https://github.com/PennyLaneAI/pennylane/pull/4075)

* Accelerate Jordan-Wigner transforms caching Pauli gate objects.
  [(#4046)](https://github.com/PennyLaneAI/pennylane/pull/4046)

* The `qchem.molecular_hamiltonian` function is upgraded to support custom wires for constructing
  differentiable Hamiltonians. The zero imaginary component of the Hamiltonian coefficients are
  removed.
  [(#4050)](https://github.com/PennyLaneAI/pennylane/pull/4050)
  [(#4094)](https://github.com/PennyLaneAI/pennylane/pull/4094)

* An error is now raised by `qchem.molecular_hamiltonian` when the `dhf` method is used for an 
  open-shell system. This duplicates a similar error in `qchem.Molecule` but makes it easier to
  inform the users that the `pyscf` backend can be used for open-shell calculations.
  [(#4058)](https://github.com/PennyLaneAI/pennylane/pull/4058)

* Added a `shots` property to `QuantumScript`. This will allow shots to be tied to executions instead of devices more
  concretely.
  [(#4067)](https://github.com/PennyLaneAI/pennylane/pull/4067)
  [(#4103)](https://github.com/PennyLaneAI/pennylane/pull/4103)
  [(#4106)](https://github.com/PennyLaneAI/pennylane/pull/4106)
  [(#4112)](https://github.com/PennyLaneAI/pennylane/pull/4112)

* Integrated `QuantumScript.shots` with `QNode` so that shots are placed on the `QuantumScript`
  during `QNode` construction.
  [(#4110)](https://github.com/PennyLaneAI/pennylane/pull/4110)

* `qml.specs` is compatible with custom operations that have `depth` bigger than 1.
  [(#4033)](https://github.com/PennyLaneAI/pennylane/pull/4033)

* `qml.Tracker` when used with `default.qubit` or `null.qubit` devices, will track resources of the quantum circuit.
  [#(4045)](https://github.com/PennyLaneAI/pennylane/pull/4045)
  [(#4110)](https://github.com/PennyLaneAI/pennylane/pull/4110)

* `qml.prod` now accepts a single qfunc input for creating new `Prod` operators.
  [(#4011)](https://github.com/PennyLaneAI/pennylane/pull/4011)

* Added `__repr__` and `__str__` methods to the `Shots` class.
  [(#4081)](https://github.com/PennyLaneAI/pennylane/pull/4081)

* Added `__eq__` and `__hash__` methods to the `Shots` class.
  [(#4082)](https://github.com/PennyLaneAI/pennylane/pull/4082)

* Added a function `measure_with_samples` that returns a sample-based measurement result given a state
  [(#4083)](https://github.com/PennyLaneAI/pennylane/pull/4083)
  [(#4093)](https://github.com/PennyLaneAI/pennylane/pull/4093)
  [(#4162)](https://github.com/PennyLaneAI/pennylane/pull/4162)
  [(#4254)](https://github.com/PennyLaneAI/pennylane/pull/4254)

* Wrap all objects being queued in an `AnnotatedQueue` so that `AnnotatedQueue` is not dependent on
  the hash of any operators/measurement processes.
  [(#4087)](https://github.com/PennyLaneAI/pennylane/pull/4087)

* All drawing methods changed their default value for the keyword argument `show_matrices`
  to `True`. This allows quick insights into broadcasted tapes for example.
  [(#3920)](https://github.com/PennyLaneAI/pennylane/pull/3920)

* Support for adjoint differentiation has been added to the `DefaultQubit2` device.
  [(#4037)](https://github.com/PennyLaneAI/pennylane/pull/4037)

* Support for sample-based measurements has been added to the `DefaultQubit2` device.
  [(#4105)](https://github.com/PennyLaneAI/pennylane/pull/4105)
  [(#4114)](https://github.com/PennyLaneAI/pennylane/pull/4114)
  [(#4133)](https://github.com/PennyLaneAI/pennylane/pull/4133)
  [(#4172)](https://github.com/PennyLaneAI/pennylane/pull/4172)

* Added a keyword argument `seed` to the `DefaultQubit2` device.
  [(#4120)](https://github.com/PennyLaneAI/pennylane/pull/4120)

* Added a `dense` keyword to `ParametrizedEvolution` that allows forcing dense or sparse matrices.
  [(#4079)](https://github.com/PennyLaneAI/pennylane/pull/4079)
  [(#4095)](https://github.com/PennyLaneAI/pennylane/pull/4095)

* Adds the Type variables `pennylane.typing.Result` and `pennylane.typing.ResultBatch` for type hinting the result of
  an execution.
  [(#4018)](https://github.com/PennyLaneAI/pennylane/pull/4108)

* `qml.devices.ExecutionConfig` no longer has a `shots` property, as it is now on the `QuantumScript`.  It now has a `use_device_gradient` property. `ExecutionConfig.grad_on_execution = None` indicates a request for `"best"`, instead of a string.
[(#4102)](https://github.com/PennyLaneAI/pennylane/pull/4102)

* `DefaultQubit2.preprocess` now returns a new `ExecutionConfig` object with decisions for `gradient_method`,
  `use_device_gradient`, and `grad_on_execution`.
  [(#4102)](https://github.com/PennyLaneAI/pennylane/pull/4102)

* `pulse.ParametrizedEvolution` now uses _batched_ compressed sparse row (`BCSR`) format. This allows computing Jacobians of the unitary directly even when `dense=False`.
  ```python
  def U(params):
      H = jnp.polyval * qml.PauliZ(0) # time dependent Hamiltonian
      Um = qml.evolve(H)(params, t=10., dense=False)
      return qml.matrix(Um)
  params = jnp.array([[0.5]], dtype=complex)
  jac = jax.jacobian(U, holomorphic=True)(params)
  ```
  [(#4126)](https://github.com/PennyLaneAI/pennylane/pull/4126)

* Updated `pennylane/qnode.py` to support parameter-shift differentiation on qutrit devices.
  ([#2845])(https://github.com/PennyLaneAI/pennylane/pull/2845)

* The new device interface in integrated with `qml.execute` for autograd, backpropagation, and no differentiation.
  [(#3903)](https://github.com/PennyLaneAI/pennylane/pull/3903)

* `CZ` now inherits from the `ControlledOp` class. It now supports exponentiation to arbitrary powers with `pow`, which is no longer limited to integers. It also supports `sparse_matrix` and `decomposition` representations.
  [(#4117)](https://github.com/PennyLaneAI/pennylane/pull/4117)

* The construction of the pauli representation for the `Sum` class is now faster.
  [(#4142)](https://github.com/PennyLaneAI/pennylane/pull/4142)

* `qchem.molecular_hamiltonian()` will now return an arithmetic operator if `enable_new_opmath()` is active.
  [(#4159)](https://github.com/PennyLaneAI/pennylane/pull/4159)

* `qchem.qubit_observable()` will now return an arithmetic operator if `enable_new_opmath()` is active. 
  [(#4138)](https://github.com/PennyLaneAI/pennylane/pull/4138)

* `qml.drawer.drawable_layers.drawable_layers` and `qml.CircuitGraph` have been updated to not rely on `Operator`
  equality or hash to work correctly.
  [(#4143)](https://github.com/PennyLaneAI/pennylane/pull/4143)

* Updated the `gradients` module to use the new `Shots` object internally.
  [(#4152)](https://github.com/PennyLaneAI/pennylane/pull/4152)

* The new device interface in integrated with `qml.execute` for Jax.
  [(#4137)](https://github.com/PennyLaneAI/pennylane/pull/4137)

* The experimental device `devices.experimental.DefaultQubit2` now supports `qml.Snapshot`.
  [(#4193)](https://github.com/PennyLaneAI/pennylane/pull/4193)

* `qml.CY` has been moved from `qml.ops.qubit.non_parametric_ops` to `qml.ops.op_math.controlled_ops`
  and now inherits from `qml.ops.op_math.ControlledOp`.
  [(#4116)](https://github.com/PennyLaneAI/pennylane/pull/4116/)

* `qml.dipole_moment()` will now return an arithmetic operator if `enable_new_opmath()` is active.
  [(#4189)](https://github.com/PennyLaneAI/pennylane/pull/4189)

* Added `qml.math.reduce_dm` and `qml.math.reduce_statevector` to produce reduced density matrices.
  Both functions have broadcasting support.
  [(#4173)](https://github.com/PennyLaneAI/pennylane/pull/4173)

* One qubit unitaries can now be decomposed into a `ZXZ` gate sequence (apart from the pre-existing `XYX` and `ZYZ`).
  [(#4210)](https://github.com/PennyLaneAI/pennylane/pull/4210)

* Added broadcasting support for `qml.math.purity`, `qml.math.vn_entropy`, `qml.math.mutual_info`, `qml.math.fidelity`,
  `qml.math.relative_entropy`, `qml.math.max_entropy`, and `qml.math.sqrt_matrix`.
  [(#4186)](https://github.com/PennyLaneAI/pennylane/pull/4186)

* Added a transform dispatcher.
  [(#4109)](https://github.com/PennyLaneAI/pennylane/pull/4109)
  
* Added a transform program.
  [(#4187)](https://github.com/PennyLaneAI/pennylane/pull/4187)

* Added broadcasting support for `qml.qinfo.reduced_dm`, `qml.qinfo.purity`, `qml.qinfo.vn_entropy`,
  `qml.qinfo.mutual_info`, `qml.qinfo.fidelity`, `qml.qinfo.relative_entropy`, and `qml.qinfo.trace_distance`.
  [(#4234)](https://github.com/PennyLaneAI/pennylane/pull/4234)

* Fix unclear documentation and indicate variable-length argument lists of functions and methods in
  the respective docstrings.
  [(#4242)](https://github.com/PennyLaneAI/pennylane/pull/4242)

* Added the ability to draw mid-circuit measurements connected by classical control signals
  to conditional operations.
  [(#4228)](https://github.com/PennyLaneAI/pennylane/pull/4228)

* The new device interface in integrated with `qml.execute` for Torch.
  [(#4257)](https://github.com/PennyLaneAI/pennylane/pull/4257)

<h4>Trace distance is now available in qml.qinfo 💥</h4>

* The quantum information module now supports computation of [trace distance](https://en.wikipedia.org/wiki/Trace_distance).
  [(#4181)](https://github.com/PennyLaneAI/pennylane/pull/4181)

  We've enabled two cases for calculating the trace distance:
  
  - A QNode transform via `qml.qinfo.trace_distance`:

    ```python
    dev = qml.device('default.qubit', wires=2)

    @qml.qnode(dev)
    def circuit(param):
        qml.RY(param, wires=0)
        qml.CNOT(wires=[0, 1])
        return qml.state()
    ```

    ```pycon
    >>> trace_distance_circuit = qml.qinfo.trace_distance(circuit, circuit, wires0=[0], wires1=[0])
    >>> x, y = np.array(0.4), np.array(0.6)
    >>> trace_distance_circuit((x,), (y,))
    0.047862689546603415
    ```

  - Support in `qml.math` for flexible post-processing:

    ```pycon
    >>> rho = np.array([[0.3, 0], [0, 0.7]])
    >>> sigma = np.array([[0.5, 0], [0, 0.5]])
    >>> qml.math.trace_distance(rho, sigma)
    0.19999999999999998
    ```

* The `qml.qnn.KerasLayer` and `qml.qnn.TorchLayer` classes now natively support parameter broadcasting.
  [(#4131)](https://github.com/PennyLaneAI/pennylane/pull/4131)

* `qchem.import_operator()` will now return an arithmetic operator if `enable_new_opmath()` is active.
  [(#4204)](https://github.com/PennyLaneAI/pennylane/pull/4204)

* Updated repr for ParametrizedHamiltonian.
  [(#4176)](https://github.com/PennyLaneAI/pennylane/pull/4176)

* The new device interface is integrated with `qml.execute` for Tensorflow.
  [(#4169)](https://github.com/PennyLaneAI/pennylane/pull/4169)

* Updated various qubit tapering methods to support operator arithmetic.
  [(#4252)](https://github.com/PennyLaneAI/pennylane/pull/4252)

<h3>Breaking changes 💔</h3>

* All drawing methods changed their default value for the keyword argument `show_matrices` to `True`.
  [(#3920)](https://github.com/PennyLaneAI/pennylane/pull/3920)

* `DiagonalQubitUnitary` does not decompose into `QubitUnitary` any longer, but into `RZ`, `IsingZZ`
  and `MultiRZ` gates.
  [(#4035)](https://github.com/PennyLaneAI/pennylane/pull/4035)

* Jax trainable parameters are now `Tracer` instead of `JVPTracer`, it is not always the right definition for the JIT 
  interface, but we update them in the custom JVP using symbolic zeros.
  [(4075)](https://github.com/PennyLaneAI/pennylane/pull/4075)

* The experimental Device interface `qml.devices.experimental.Device` now requires that the `preprocess` method
  also returns an `ExecutionConfig` object. This allows the device to choose what `"best"` means for various
  hyperparameters like `gradient_method` and `grad_on_execution`.
  [(#4007)](https://github.com/PennyLaneAI/pennylane/pull/4007)
  [(#4102)](https://github.com/PennyLaneAI/pennylane/pull/4102)

* Gradient transforms with Jax do not support `argnum` anymore,  `argnums` needs to be used.
  [(#4076)](https://github.com/PennyLaneAI/pennylane/pull/4076)

* `pennylane.collections`, `pennylane.op_sum`, and `pennylane.utils.sparse_hamiltonian` are removed.

* The `pennylane.transforms.qcut` module now uses `(op, id(op))` as nodes in directed multigraphs that are used within
  the circuit cutting workflow instead of `op`. This change removes the dependency of the module on the hash of operators.
  [(#4227)](https://github.com/PennyLaneAI/pennylane/pull/4227)

* `Operator.data` now returns a `tuple` instead of a `list`.
  [(#4222)](https://github.com/PennyLaneAI/pennylane/pull/4222)

<h3>Deprecations 👋</h3>

* `LieAlgebraOptimizer` is renamed. Please use `RiemannianGradientOptimizer` instead.
  [(#4153)(https://github.com/PennyLaneAI/pennylane/pull/4153)]

* `Operation.base_name` is deprecated. Please use `Operation.name` or `type(op).__name__` instead.

* `QuantumScript`'s `name` keyword argument and property are deprecated.
  This also affects `QuantumTape` and `OperationRecorder`.
  [(#4141)](https://github.com/PennyLaneAI/pennylane/pull/4141)

* `qml.grouping` module is removed. The functionality has been reorganized in the `qml.pauli` module.

* The public methods of `DefaultQubit` are pending changes to follow the new device API, as used in
  `DefaultQubit2`. Warnings have been added to the docstrings to reflect this.
  [(#4145)](https://github.com/PennyLaneAI/pennylane/pull/4145)

* `qml.math.reduced_dm` has been deprecated. Please use `qml.math.reduce_dm` or `qml.math.reduce_statevector` instead.
  [(#4173)](https://github.com/PennyLaneAI/pennylane/pull/4173)

* `qml.math.purity`, `qml.math.vn_entropy`, `qml.math.mutual_info`, `qml.math.fidelity`,
  `qml.math.relative_entropy`, and `qml.math.max_entropy` no longer support state vectors as
  input. Please call `qml.math.dm_from_state_vector` on the input before passing to any of these functions.
  [(#4186)](https://github.com/PennyLaneAI/pennylane/pull/4186)

* `do_queue` keyword argument in `qml.operation.Operator` is deprecated. Instead of
  setting `do_queue=False`, use the `qml.QueuingManager.stop_recording()` context.
  [(#4148)](https://github.com/PennyLaneAI/pennylane/pull/4148)

* `zyz_decomposition` and `xyx_decomposition` are now deprecated in favour of `one_qubit_decomposition`.
  [(#4230)](https://github.com/PennyLaneAI/pennylane/pull/4230)

<h3>Documentation 📝</h3>

* The documentation is updated to construct `QuantumTape` upon initialization instead of with queuing.
  [(#4243)](https://github.com/PennyLaneAI/pennylane/pull/4243)

* The docstring for `qml.ops.op_math.Pow.__new__` is now complete and it has been updated along with
  `qml.ops.op_math.Adjoint.__new__`.
  [(#4231)](https://github.com/PennyLaneAI/pennylane/pull/4231)

* The docstring for `qml.grad` now states that it should be used with the Autograd interface only.
  [(#4202)](https://github.com/PennyLaneAI/pennylane/pull/4202)

* The description of `mult` in the `qchem.Molecule` docstring now correctly states the value
  of `mult` that is supported.
  [(#4058)](https://github.com/PennyLaneAI/pennylane/pull/4058)

<h3>Bug fixes 🐛</h3>

* Fixes the matrix of `SProd` when the coefficient is tensorflow and the target matrix is not `complex128`.
  [(#4249)](https://github.com/PennyLaneAI/pennylane/pull/4249)

* Fixes adjoint jacobian results with `grad_on_execution=False` in the JAX-JIT interface.
  [(4217)](https://github.com/PennyLaneAI/pennylane/pull/4217)

* Fixes a bug where `stoch_pulse_grad` would ignore prefactors of rescaled Pauli words in the
  generating terms of a pulse Hamiltonian.
  [(4156)](https://github.com/PennyLaneAI/pennylane/pull/4156)
  
* Fixes a bug where the wire ordering of the `wires` argument to `qml.density_matrix`
  was not taken into account.
  [(#4072)](https://github.com/PennyLaneAI/pennylane/pull/4072)

* Removes a patch in `interfaces/autograd.py` that checks for the `strawberryfields.gbs` device.  That device
  is pinned to PennyLane <= v0.29.0, so that patch is no longer necessary.

* `qml.pauli.are_identical_pauli_words` now treats all identities as equal. Identity terms on Hamiltonians with non-standard
  wire orders are no longer eliminated.
  [(#4161)](https://github.com/PennyLaneAI/pennylane/pull/4161)

* `qml.pauli_sentence()` is now compatible with empty Hamiltonians `qml.Hamiltonian([], [])`.
  [(#4171)](https://github.com/PennyLaneAI/pennylane/pull/4171)

* Fixes a bug with Jax where executing multiple tapes with `gradient_fn="device"` would fail.
  [(#4190)](https://github.com/PennyLaneAI/pennylane/pull/4190)

* A more meaningful error message is raised when broadcasting with adjoint differentation on `DefaultQubit`.
  [(#4203)](https://github.com/PennyLaneAI/pennylane/pull/4203)
  
* The `has_unitary_generator` attribute in `qml.ops.qubit.attributes` no longer contains operators with non-unitary generators.
  [(#4183)](https://github.com/PennyLaneAI/pennylane/pull/4183)

* Fixes a bug where `op = qml.qsvt()` was incorrect up to a global phase when using `convention="Wx""` and `qml.matrix(op)`.
  [(#4214)](https://github.com/PennyLaneAI/pennylane/pull/4214)

* Fixed buggy calculation of angle in `xyx_decomposition` causing it to give an incorrect decomposition.
  An if conditional was intended to prevent divide by zero errors but the division was by the sine of the argument so any multiple of $\pi$ should trigger the conditional, but it was only checking if the argument was 0. Example: `qml.Rot(2.3, 2.3, 2.3)`
  [(#4210)](https://github.com/PennyLaneAI/pennylane/pull/4210)

* Allow for `Sum` observables with trainable parameters.
  [(#4251)](https://github.com/PennyLaneAI/pennylane/pull/4251)

<h3>Contributors ✍️</h3>

This release contains contributions from (in alphabetical order):

Venkatakrishnan AnushKrishna
Utkarsh Azad
Isaac De Vlugt,
Lillian M. A. Frederiksen,
Emiliano Godinez Ramirez
Nikhil Harle
Soran Jahangiri,
Edward Jiang,
Korbinian Kottmann,
Christina Lee,
Vincent Michaud-Rioux,
Romain Moyard,
Tristan Nemoz,
Mudit Pandey,
Borja Requena,
Mainak Roy,
Matthew Silverman,
Jay Soni,
Edward Thomas,
David Wierichs,
Frederik Wilde.