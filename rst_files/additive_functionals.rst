.. _additive_functionals:

.. include:: /_static/includes/lecture_howto_py.raw

.. highlight:: python3


***********************
Additive Functionals
***********************


.. index::
    single: Models; Additive functionals

.. contents:: :depth: 2


**Co-authors: Chase Coleman and Balint Szoke**

Overview
=============

Some time series are nonstationary

For example, output, prices, and dividends are typically nonstationary, due to irregular but persistent growth

Which kinds of models are useful for studying such time series?

Hansen and Scheinkman :cite:`Hans_Scheink_2009` analyze two classes of time series models that accommodate growth

They are:

#.  **additive functionals** that display random "arithmetic growth"

#.  **multiplicative functionals** that display random "geometric growth"

These two classes of processes are closely connected

For example, if a process :math:`\{y_t\}` is an additive functional and :math:`\phi_t = \exp(y_t)`, then :math:`\{\phi_t\}` is a multiplicative functional

Hansen and Sargent :cite:`Hans_Sarg_book_2016` (chs. 5 and 6) describe discrete time versions of additive and multiplicative functionals

In this lecture we discuss the former (i.e., additive functionals)

In the :doc:`next lecture <multiplicative_functionals>` we discuss multiplicative functionals

We also consider fruitful decompositions of additive and multiplicative processes, a more in depth discussion of which can be found in Hansen and Sargent :cite:`Hans_Sarg_book_2016` 


A Particular Additive Functional
====================================

This lecture focuses on a particular type of additive functional: a scalar process :math:`\{y_t\}_{t=0}^\infty` whose increments are driven by a Gaussian vector autoregression

It is simple to construct, simulate, and analyze

This additive functional consists of two components, the first of which is a **first-order vector autoregression** (VAR) 

.. math::
    :label: old1_additive_functionals

    x_{t+1} = A x_t + B z_{t+1} 


Here 

* :math:`x_t` is an :math:`n \times 1` vector, 
  
* :math:`A` is an :math:`n \times n` stable matrix (all eigenvalues lie within the open unit circle),
  
* :math:`z_{t+1} \sim {\cal N}(0,I)` is an :math:`m \times 1` i.i.d. shock,
  
* :math:`B` is an :math:`n \times m` matrix, and

* :math:`x_0 \sim {\cal N}(\mu_0, \Sigma_0)` is a random initial condition for :math:`x`

The second component is an equation that expresses increments
of :math:`\{y_t\}_{t=0}^\infty` as linear functions of 

* a scalar constant :math:`\nu`, 
  
* the vector :math:`x_t`, and 
  
* the same Gaussian vector :math:`z_{t+1}` that appears in the VAR :eq:`old1_additive_functionals`

In particular,

.. math::
    :label: old2_additive_functionals

    y_{t+1} - y_{t} = \nu + D x_{t} + F z_{t+1}  


Here :math:`y_0 \sim {\cal N}(\mu_{y0}, \Sigma_{y0})` is a random
initial condition

The nonstationary random process :math:`\{y_t\}_{t=0}^\infty` displays
systematic but random *arithmetic growth*


A linear state space representation
------------------------------------

One way to represent the overall dynamics is to use a :doc:`linear state space system <linear_models>`

To do this, we set up state and observation vectors 

.. math::

    \hat{x}_t = \begin{bmatrix} 1 \\  x_t \\ y_t  \end{bmatrix} 
    \quad \text{and} \quad
    \hat{y}_t = \begin{bmatrix} x_t \\ y_t  \end{bmatrix}


Now we construct the state space system

.. math::

     \begin{bmatrix} 
         1 \\ 
         x_{t+1} \\ 
         y_{t+1}  
     \end{bmatrix} 
     = 
     \begin{bmatrix} 
        1 & 0 & 0  \\ 
        0  & A & 0 \\ 
        \nu & D' &  1 \\ 
    \end{bmatrix} 
    \begin{bmatrix} 
        1 \\ 
        x_t \\ 
        y_t 
    \end{bmatrix} + 
    \begin{bmatrix} 
        0 \\  B \\ F'  
    \end{bmatrix} 
    z_{t+1} 


.. math::

    \begin{bmatrix} 
        x_t \\ 
        y_t  
    \end{bmatrix} 
    = \begin{bmatrix} 
        0  & I & 0  \\ 
        0 & 0  & 1 
    \end{bmatrix} 
    \begin{bmatrix} 
        1 \\  x_t \\ y_t  
    \end{bmatrix}


This can be written as

.. math::

    \begin{aligned}
      \hat{x}_{t+1} &= \hat{A} \hat{x}_t + \hat{B} z_{t+1} \\
      \hat{y}_{t} &= \hat{D} \hat{x}_t
    \end{aligned}


which is a standard linear state space system

To study it, we could map it into an instance of `LinearStateSpace <https://github.com/QuantEcon/QuantEcon.py/blob/master/quantecon/lss.py>`_ from `QuantEcon.py <http://quantecon.org/python_index.html>`_ 

We will in fact use a different set of code for simulation, for reasons described below




Dynamics
============

Let's run some simulations to build intuition

.. _addfunc_eg1:

In doing so we'll assume that :math:`z_{t+1}` is scalar and that :math:`\tilde x_t` follows a 4th-order scalar autoregession

.. math::
    :label: ftaf

    \tilde x_{t+1} = \phi_1 \tilde x_{t} + \phi_2 \tilde x_{t-1} 
    + \phi_3 \tilde x_{t-2}
    + \phi_4 \tilde x_{t-3} + \sigma z_{t+1} 


Let the increment in :math:`\{y_t\}` obey

.. math::

    y_{t+1} - y_t =  \nu + \tilde x_t + \sigma z_{t+1}  


with an initial condition for :math:`y_0`

While :eq:`ftaf` is not a first order system like :eq:`old1_additive_functionals`, we know that it can be mapped  into a first order system

* for an example of such a mapping, see :ref:`this example <lss_sode>` 


In fact this whole model can be mapped into the additive functional system definition in :eq:`old1_additive_functionals` -- :eq:`old2_additive_functionals`  by appropriate selection of the matrices :math:`A, B, D, F`

You can try writing these matrices down now as an exercise --- the correct expressions will appear in the code below

Simulation
---------------

When simulating we embed our variables into a bigger system 

This system also constructs the components of the decompositions of :math:`y_t` and of :math:`\exp(y_t)` proposed by Hansen and Scheinkman :cite:`Hans_Scheink_2009`


All of these objects are computed using the code below


.. code-block:: python3

    """ 
    @authors: Chase Coleman, Balint Szoke, Tom Sargent

    """

    import numpy as np
    import scipy as sp
    import scipy.linalg as la
    import quantecon as qe
    import matplotlib.pyplot as plt
    from scipy.stats import norm, lognorm


    class AMF_LSS_VAR:
        """
        This class transforms an additive (multiplicative)
        functional into a QuantEcon linear state space system.
        """

        def __init__(self, A, B, D, F=None, ν=None):
            # Unpack required elements
            self.nx, self.nk = B.shape
            self.A, self.B = A, B

            # checking the dimension of D (extended from the scalar case)
            if len(D.shape) > 1 and D.shape[0] != 1:
                self.nm = D.shape[0]
                self.D = D
            elif len(D.shape) > 1 and D.shape[0] == 1:
                self.nm = 1
                self.D = D
            else:
                self.nm = 1
                self.D = np.expand_dims(D, 0)
    
            # Create space for additive decomposition
            self.add_decomp = None
            self.mult_decomp = None
    
            # Set F
            if not np.any(F):
                self.F = np.zeros((self.nk, 1))
            else:
                self.F = F

            # Set ν
            if not np.any(ν):
                self.ν = np.zeros((self.nm, 1))
            elif type(ν) == float:
                self.ν = np.asarray([[ν]])
            elif len(ν.shape) == 1:
                self.ν = np.expand_dims(ν, 1)
            else:
                self.ν = ν

            if self.ν.shape[0] != self.D.shape[0]:
                raise ValueError("The dimension of ν is inconsistent with D!")

            # Construct BIG state space representation
            self.lss = self.construct_ss()

        def construct_ss(self):
            """
            This creates the state space representation that can be passed
            into the quantecon LSS class.
            """
            # Pull out useful info
            nx, nk, nm = self.nx, self.nk, self.nm
            A, B, D, F, ν = self.A, self.B, self.D, self.F, self.ν
            if self.add_decomp:
                ν, H, g = self.add_decomp
            else:
                ν, H, g = self.additive_decomp()

            # Auxiliary blocks with 0's and 1's to fill out the lss matrices
            nx0c = np.zeros((nx, 1))
            nx0r = np.zeros(nx)
            nx1 = np.ones(nx)
            nk0 = np.zeros(nk)
            ny0c = np.zeros((nm, 1))
            ny0r = np.zeros(nm)
            ny1m = np.eye(nm)
            ny0m = np.zeros((nm, nm))
            nyx0m = np.zeros_like(D)
    
            # Build A matrix for LSS
            # Order of states is: [1, t, xt, yt, mt]
            A1 = np.hstack([1, 0, nx0r, ny0r, ny0r])            # Transition for 1
            A2 = np.hstack([1, 1, nx0r, ny0r, ny0r])            # Transition for t
            A3 = np.hstack([nx0c, nx0c, A, nyx0m.T, nyx0m.T])   # Transition for x_{t+1}
            A4 = np.hstack([ν, ny0c, D, ny1m, ny0m])            # Transition for y_{t+1}
            A5 = np.hstack([ny0c, ny0c, nyx0m, ny0m, ny1m])     # Transition for m_{t+1}
            Abar = np.vstack([A1, A2, A3, A4, A5])
    
            # Build B matrix for LSS
            Bbar = np.vstack([nk0, nk0, B, F, H])
    
            # Build G matrix for LSS
            # Order of observation is: [xt, yt, mt, st, tt]
            G1 = np.hstack([nx0c, nx0c, np.eye(nx), nyx0m.T, nyx0m.T])    # Selector for x_{t}
            G2 = np.hstack([ny0c, ny0c, nyx0m, ny1m, ny0m])               # Selector for y_{t}
            G3 = np.hstack([ny0c, ny0c, nyx0m, ny0m, ny1m])               # Selector for martingale
            G4 = np.hstack([ny0c, ny0c, -g, ny0m, ny0m])                  # Selector for stationary
            G5 = np.hstack([ny0c, ν, nyx0m, ny0m, ny0m])                  # Selector for trend
            Gbar = np.vstack([G1, G2, G3, G4, G5])

            # Build H matrix for LSS
            Hbar = np.zeros((Gbar.shape[0], nk))
    
            # Build LSS type
            x0 = np.hstack([1, 0, nx0r, ny0r, ny0r])
            S0 = np.zeros((len(x0), len(x0)))
            lss = qe.lss.LinearStateSpace(Abar, Bbar, Gbar, Hbar, mu_0=x0, Sigma_0=S0)

            return lss

        def additive_decomp(self):
            """
            Return values for the martingale decomposition 
                - ν         : unconditional mean difference in Y
                - H         : coefficient for the (linear) martingale component (κ_a)
                - g         : coefficient for the stationary component g(x)
                - Y_0       : it should be the function of X_0 (for now set it to 0.0)
            """
            I = np.identity(self.nx)
            A_res = la.solve(I - self.A, I)
            g = self.D @ A_res
            H = self.F + self.D @ A_res @ self.B

            return self.ν, H, g

        def multiplicative_decomp(self):
            """
            Return values for the multiplicative decomposition (Example 5.4.4.)
                - ν_tilde  : eigenvalue
                - H        : vector for the Jensen term
            """
            ν, H, g = self.additive_decomp()
            ν_tilde = ν + (.5)*np.expand_dims(np.diag(H @ H.T), 1)

            return ν_tilde, H, g

        def loglikelihood_path(self, x, y):
            A, B, D, F = self.A, self.B, self.D, self.F
            k, T = y.shape
            FF = F @ F.T
            FFinv = la.inv(FF)
            temp = y[:, 1:] - y[:, :-1] - D @ x[:, :-1]
            obs =  temp * FFinv * temp
            obssum = np.cumsum(obs)
            scalar = (np.log(la.det(FF)) + k*np.log(2*np.pi))*np.arange(1, T)

            return -(.5)*(obssum + scalar)
    
        def loglikelihood(self, x, y):
            llh = self.loglikelihood_path(x, y)

            return llh[-1]


        def plot_additive(self, T, npaths=25, show_trend=True):
            """
            Plots for the additive decomposition
    
            """
            # Pull out right sizes so we know how to increment
            nx, nk, nm = self.nx, self.nk, self.nm

            # Allocate space (nm is the number of additive functionals - we want npaths for each)
            mpath = np.empty((nm*npaths, T))
            mbounds = np.empty((nm*2, T))
            spath = np.empty((nm*npaths, T))
            sbounds = np.empty((nm*2, T))
            tpath = np.empty((nm*npaths, T))
            ypath = np.empty((nm*npaths, T))

            # Simulate for as long as we wanted
            moment_generator = self.lss.moment_sequence()
            # Pull out population moments
            for t in range (T):
                tmoms = next(moment_generator)
                ymeans = tmoms[1]
                yvar = tmoms[3]

                # Lower and upper bounds - for each additive functional
                for ii in range(nm):
                    li, ui = ii*2, (ii+1)*2
                    madd_dist = norm(ymeans[nx+nm+ii], np.sqrt(yvar[nx+nm+ii, nx+nm+ii]))
                    mbounds[li:ui, t] = madd_dist.ppf([0.01, .99])

                    sadd_dist = norm(ymeans[nx+2*nm+ii], np.sqrt(yvar[nx+2*nm+ii, nx+2*nm+ii]))
                    sbounds[li:ui, t] = sadd_dist.ppf([0.01, .99])

            # Pull out paths
            for n in range(npaths):
                x, y = self.lss.simulate(T)
                for ii in range(nm):
                    ypath[npaths*ii+n, :] = y[nx+ii, :]
                    mpath[npaths*ii+n, :] = y[nx+nm + ii, :]
                    spath[npaths*ii+n, :] = y[nx+2*nm + ii, :]
                    tpath[npaths*ii+n, :] = y[nx+3*nm + ii, :]

            add_figs = []

            for ii in range(nm):
                li, ui = npaths*(ii), npaths*(ii+1)
                LI, UI = 2*(ii), 2*(ii+1)
                add_figs.append(self.plot_given_paths(T, ypath[li:ui,:], mpath[li:ui,:], spath[li:ui,:],
                                                      tpath[li:ui,:], mbounds[LI:UI,:], sbounds[LI:UI,:],
                                                      show_trend=show_trend))

                add_figs[ii].suptitle(f'Additive decomposition of $y_{ii+1}$', fontsize=14)

            return add_figs


        def plot_multiplicative(self, T, npaths=25, show_trend=True):
            """
            Plots for the multiplicative decomposition

            """
            # Pull out right sizes so we know how to increment
            nx, nk, nm = self.nx, self.nk, self.nm
            # Matrices for the multiplicative decomposition
            ν_tilde, H, g = self.multiplicative_decomp()

            # Allocate space (nm is the number of functionals - we want npaths for each)
            mpath_mult = np.empty((nm*npaths, T))
            mbounds_mult = np.empty((nm*2, T))
            spath_mult = np.empty((nm*npaths, T))
            sbounds_mult = np.empty((nm*2, T))
            tpath_mult = np.empty((nm*npaths, T))
            ypath_mult = np.empty((nm*npaths, T))

            # Simulate for as long as we wanted
            moment_generator = self.lss.moment_sequence()
            # Pull out population moments
            for t in range(T):
                tmoms = next(moment_generator)
                ymeans = tmoms[1]
                yvar = tmoms[3]

                # Lower and upper bounds - for each multiplicative functional
                for ii in range(nm):
                    li, ui = ii*2, (ii+1)*2
                    Mdist = lognorm(np.asscalar(np.sqrt(yvar[nx+nm+ii, nx+nm+ii])), 
                                    scale=np.asscalar( np.exp( ymeans[nx+nm+ii]- \
                                                    t*(.5)*np.expand_dims(np.diag(H @ H.T),1)[ii])))
                    Sdist = lognorm(np.asscalar(np.sqrt(yvar[nx+2*nm+ii, nx+2*nm+ii])),
                                    scale = np.asscalar( np.exp(-ymeans[nx+2*nm+ii])))
                    mbounds_mult[li:ui, t] = Mdist.ppf([.01, .99])
                    sbounds_mult[li:ui, t] = Sdist.ppf([.01, .99])

            # Pull out paths
            for n in range(npaths):
                x, y = self.lss.simulate(T)
                for ii in range(nm):
                    ypath_mult[npaths*ii+n, :] = np.exp(y[nx+ii, :])
                    mpath_mult[npaths*ii+n, :] = np.exp(y[nx+nm + ii, :] - np.arange(T)*(.5)*np.expand_dims(np.diag(H @ H.T),1)[ii])
                    spath_mult[npaths*ii+n, :] = 1/np.exp(-y[nx+2*nm + ii, :])
                    tpath_mult[npaths*ii+n, :] = np.exp(y[nx+3*nm + ii, :] + np.arange(T)*(.5)*np.expand_dims(np.diag(H @ H.T),1)[ii])

            mult_figs = []

            for ii in range(nm):
                li, ui = npaths*(ii), npaths*(ii+1)
                LI, UI = 2*(ii), 2*(ii+1)

                mult_figs.append(self.plot_given_paths(T, ypath_mult[li:ui,:], mpath_mult[li:ui,:], 
                                                       spath_mult[li:ui,:], tpath_mult[li:ui,:], 
                                                       mbounds_mult[LI:UI,:], sbounds_mult[LI:UI,:], 1, 
                                                       show_trend=show_trend))
                mult_figs[ii].suptitle(f'Multiplicative decomposition of $y_{ii+1}$', fontsize=14)

            return mult_figs

        def plot_martingales(self, T, npaths=25):

            # Pull out right sizes so we know how to increment
            nx, nk, nm = self.nx, self.nk, self.nm
            # Matrices for the multiplicative decomposition
            ν_tilde, H, g = self.multiplicative_decomp()

            # Allocate space (nm is the number of functionals - we want npaths for each)
            mpath_mult = np.empty((nm*npaths, T))
            mbounds_mult = np.empty((nm*2, T))

            # Simulate for as long as we wanted
            moment_generator = self.lss.moment_sequence()
            # Pull out population moments
            for t in range (T):
                tmoms = next(moment_generator)
                ymeans = tmoms[1]
                yvar = tmoms[3]

                # Lower and upper bounds - for each functional
                for ii in range(nm):
                    li, ui = ii*2, (ii+1)*2
                    Mdist = lognorm(np.asscalar(np.sqrt(yvar[nx+nm+ii, nx+nm+ii])), 
                                    scale=np.asscalar( np.exp( ymeans[nx+nm+ii]- \
                                                    t*(.5)*np.expand_dims(np.diag(H @ H.T),1)[ii])))
                    mbounds_mult[li:ui, t] = Mdist.ppf([.01, .99])

            # Pull out paths
            for n in range(npaths):
                x, y = self.lss.simulate(T)
                for ii in range(nm):
                    mpath_mult[npaths*ii+n, :] = np.exp(y[nx+nm + ii, :] - np.arange(T)*(.5)*np.expand_dims(np.diag(H @ H.T),1)[ii])

            mart_figs = []

            for ii in range(nm):
                li, ui = npaths*(ii), npaths*(ii+1)
                LI, UI = 2*(ii), 2*(ii+1)
                mart_figs.append(self.plot_martingale_paths(T, mpath_mult[li:ui, :],
                                                            mbounds_mult[LI:UI, :],
                                                            horline=1))
                mart_figs[ii].suptitle(f'Martingale components for many paths of $y_{ii+1}$', fontsize=14)

            return mart_figs


        def plot_given_paths(self, T, ypath, mpath, spath, tpath,
                             mbounds, sbounds, horline=0, show_trend=True):

            # Allocate space
            trange = np.arange(T)

            # Create figure
            fig, ax = plt.subplots(2, 2, sharey=True, figsize=(15, 8))

            # Plot all paths together
            ax[0, 0].plot(trange, ypath[0, :], label="$y_t$", color="k")
            ax[0, 0].plot(trange, mpath[0, :], label="$m_t$", color="m")
            ax[0, 0].plot(trange, spath[0, :], label="$s_t$", color="g")
            if show_trend:
                ax[0, 0].plot(trange, tpath[0, :], label="$t_t$", color="r")
            ax[0, 0].axhline(horline, color="k", linestyle="-.")
            ax[0, 0].set_title("One Path of All Variables")
            ax[0, 0].legend(loc="upper left")

            # Plot Martingale Component
            ax[0, 1].plot(trange, mpath[0, :], "m")
            ax[0, 1].plot(trange, mpath.T, alpha=0.45, color="m")
            ub = mbounds[1, :]
            lb = mbounds[0, :]
            ax[0, 1].fill_between(trange, lb, ub, alpha=0.25, color="m")
            ax[0, 1].set_title("Martingale Components for Many Paths")
            ax[0, 1].axhline(horline, color="k", linestyle="-.")

            # Plot Stationary Component
            ax[1, 0].plot(spath[0, :], color="g")
            ax[1, 0].plot(spath.T, alpha=0.25, color="g")
            ub = sbounds[1, :]
            lb = sbounds[0, :]
            ax[1, 0].fill_between(trange, lb, ub, alpha=0.25, color="g")
            ax[1, 0].axhline(horline, color="k", linestyle="-.")
            ax[1, 0].set_title("Stationary Components for Many Paths")

            # Plot Trend Component
            if show_trend:
                ax[1, 1].plot(tpath.T, color="r")
            ax[1, 1].set_title("Trend Components for Many Paths")
            ax[1, 1].axhline(horline, color="k", linestyle="-.")

            return fig

        def plot_martingale_paths(self, T, mpath, mbounds,
                                  horline=1, show_trend=False):
            # Allocate space
            trange = np.arange(T)

            # Create figure
            fig, ax = plt.subplots(1, 1, figsize=(10, 6))

            # Plot Martingale Component
            ub = mbounds[1, :]
            lb = mbounds[0, :]
            ax.fill_between(trange, lb, ub, color="#ffccff")
            ax.axhline(horline, color="k", linestyle="-.")
            ax.plot(trange, mpath.T, linewidth=0.25, color="#4c4c4c")

            return fig

For now, we just plot :math:`y_t` and :math:`x_t`, postponing until later a description of exactly how we compute them

.. _addfunc_egcode:



.. code-block:: python3

    ϕ_1, ϕ_2, ϕ_3, ϕ_4 = 0.5, -0.2, 0, 0.5
    σ = 0.01
    ν = 0.01 # Growth rate
    
    # A matrix should be n x n
    A = np.array([[ϕ_1, ϕ_2, ϕ_3, ϕ_4],
                  [ 1,   0,    0,   0],
                  [ 0,   1,    0,   0],
                  [ 0,   0,    1,   0]])
    
    # B matrix should be n x k
    B = np.array([[σ, 0, 0, 0]]).T
    
    D = np.array([1, 0, 0, 0]) @ A
    F = np.array([1, 0, 0, 0]) @ B

    amf = AMF_LSS_VAR(A, B, D, F, ν=ν)

    T = 150
    x, y = amf.lss.simulate(T)
    
    fig, ax = plt.subplots(2, 1, figsize=(10, 9))
    
    ax[0].plot(np.arange(T), y[amf.nx, :], color='k')
    ax[0].set_title('A particular path of $y_t$')
    ax[1].plot(np.arange(T), y[0, :], color='g')
    ax[1].axhline(0, color='k', linestyle='-.')
    ax[1].set_title('Associated path of $x_t$')
    plt.show()




Notice the irregular but persistent growth in :math:`y_t`


Decomposition
---------------

Hansen and Sargent :cite:`Hans_Sarg_book_2016` describe how to construct a decomposition of
an additive functional into four parts:

-  a constant inherited from initial values :math:`x_0` and :math:`y_0`

-  a linear trend

-  a martingale

-  an (asymptotically) stationary component

To attain this decomposition for the particular class of additive
functionals defined by :eq:`old1_additive_functionals` and :eq:`old2_additive_functionals`, we first construct the matrices

.. math::

    \begin{aligned}
      H & := F + B'(I - A')^{-1} D 
      \\
      g & := D' (I - A)^{-1}
    \end{aligned}


Then the Hansen-Scheinkman :cite:`Hans_Scheink_2009` decomposition is

.. math::

    \begin{aligned}
      y_t 
      &= \underbrace{t \nu}_{\text{trend component}} + 
         \overbrace{\sum_{j=1}^t H z_j}^{\text{Martingale component}} - 
         \underbrace{g x_t}_{\text{stationary component}} + 
         \overbrace{g x_0 + y_0}^{\text{initial conditions}}
    \end{aligned}


At this stage you should pause and verify that :math:`y_{t+1} - y_t` satisfies :eq:`old2_additive_functionals`

It is convenient for us to introduce the following notation:

-  :math:`\tau_t = \nu t` , a linear, deterministic trend

-  :math:`m_t = \sum_{j=1}^t H z_j`, a martingale with time :math:`t+1` increment :math:`H z_{t+1}`

-  :math:`s_t = g x_t`, an (asymptotically) stationary component

We want to characterize and simulate components :math:`\tau_t, m_t, s_t` of the decomposition.

A convenient way to do this is to construct an appropriate instance of a :doc:`linear state space system <linear_models>` by using `LinearStateSpace <https://github.com/QuantEcon/QuantEcon.py/blob/master/quantecon/lss.py>`_ from `QuantEcon.py <http://quantecon.org/python_index.html>`_

This will allow us to use the routines in `LinearStateSpace <https://github.com/QuantEcon/QuantEcon.py/blob/master/quantecon/lss.py>`_ to study dynamics

To start, observe that, under the dynamics in :eq:`old1_additive_functionals` and :eq:`old2_additive_functionals` and with the
definitions just given,

.. math::

    \begin{bmatrix} 
        1 \\ 
        t+1 \\ 
        x_{t+1} \\ 
        y_{t+1} \\ 
        m_{t+1} 
    \end{bmatrix} 
    = 
    \begin{bmatrix} 
        1 & 0 & 0 & 0 & 0 \\ 
        1 & 1 & 0 & 0 & 0 \\ 
        0 & 0 & A & 0 & 0 \\ 
        \nu & 0 & D' & 1 & 0 \\ 
        0 & 0 & 0 & 0 & 1 
    \end{bmatrix} 
    \begin{bmatrix} 
        1 \\ 
        t \\ 
        x_t \\ 
        y_t \\ 
        m_t 
    \end{bmatrix} 
    +
    \begin{bmatrix} 
        0 \\ 
        0 \\ 
        B \\ 
        F' \\ 
        H' 
    \end{bmatrix} 
    z_{t+1} 


and

.. math::

    \begin{bmatrix} 
        x_t \\ 
        y_t \\ 
        \tau_t \\ 
        m_t \\ 
        s_t 
    \end{bmatrix} 
    = 
    \begin{bmatrix} 
        0 & 0 & I & 0 & 0 \\ 
        0 & 0 & 0 & 1 & 0 \\ 
        0 & \nu & 0 & 0 & 0 \\ 
        0 & 0 & 0 & 0 & 1 \\ 
        0 & 0 & -g & 0 & 0 
    \end{bmatrix} 
    \begin{bmatrix} 
        1 \\ 
        t \\ 
        x_t \\ 
        y_t \\ 
        m_t 
    \end{bmatrix}


With

.. math::

    \tilde{x} := \begin{bmatrix} 1 \\ t \\ x_t \\ y_t \\ m_t \end{bmatrix} 
    \quad \text{and} \quad
    \tilde{y} := \begin{bmatrix} x_t \\ y_t \\ \tau_t \\ m_t \\ s_t \end{bmatrix}


we can write this as the linear state space system

.. math::

    \begin{aligned}
      \tilde{x}_{t+1} &= \tilde{A} \tilde{x}_t + \tilde{B} z_{t+1} \\
      \tilde{y}_{t} &= \tilde{D} \tilde{x}_t
    \end{aligned}


By picking out components of :math:`\tilde y_t`, we can track all variables of
interest

Code
=====================

The class `AMF_LSS_VAR <https://github.com/QuantEcon/QuantEcon.lectures.code/blob/master/additive_functionals/amflss.py>`__ mentioned above does all that we want to study our additive functional

In fact `AMF_LSS_VAR <https://github.com/QuantEcon/QuantEcon.lectures.code/blob/master/additive_functionals/amflss.py>`__ does more, as we shall explain below

(A hint that it does more is the name of the class -- here AMF stands for
"additive and multiplicative functional" -- the code will do things for
multiplicative functionals too)
   
Let's use this code (embedded above) to explore the :ref:`example process described above <addfunc_eg1>`

If you run :ref:`the code that first simulated that example <addfunc_egcode>` again and then the method call
you will generate (modulo randomness) the plot



.. code-block:: python3

    amf.plot_additive(T)
    plt.show()



When we plot multiple realizations of a component in the 2nd, 3rd, and 4th panels, we also plot population 95% probability coverage sets computed using the LinearStateSpace class

We have chosen to simulate many paths, all starting from the *same* nonrandom initial conditions :math:`x_0, y_0` (you can tell this from the shape of the 95% probability coverage shaded areas)

Notice tell-tale signs of these probability coverage shaded areas

*  the purple one for the martingale component :math:`m_t` grows with
   :math:`\sqrt{t}`

*  the green one for the stationary component :math:`s_t` converges to a
   constant band


An associated multiplicative functional
------------------------------------------

Where :math:`\{y_t\}` is our additive functional, let :math:`M_t = \exp(y_t)`

As mentioned above, the process :math:`\{M_t\}` is called a **multiplicative functional**

Corresponding to the additive decomposition described above we have the multiplicative decomposition of the :math:`M_t`

.. math::

    \frac{M_t}{M_0} 
    = \exp (t \nu) \exp \Bigl(\sum_{j=1}^t H \cdot Z_j \Bigr) \exp \biggl( D'(I-A)^{-1} x_0 - D'(I-A)^{-1} x_t \biggr)  


or

.. math::

    \frac{M_t}{M_0} =  \exp\left( \tilde \nu t \right) \Biggl( \frac{\widetilde M_t}{\widetilde M_0}\Biggr) \left( \frac{\tilde e (X_0)} {\tilde e(x_t)} \right)


where

.. math::

    \tilde \nu =  \nu + \frac{H \cdot H}{2} ,
    \quad
    \widetilde M_t = \exp \biggl( \sum_{j=1}^t \biggl(H \cdot z_j -\frac{ H \cdot H }{2} \biggr) \biggr),  \quad \widetilde M_0 =1 


and

.. math::

    \tilde e(x) = \exp[g(x)] = \exp \bigl[ D' (I - A)^{-1} x \bigr]


An instance of class `AMF_LSS_VAR <https://github.com/QuantEcon/QuantEcon.lectures.code/blob/master/additive_functionals/amflss.py>`__ includes this associated multiplicative functional as an attribute

Let's plot this multiplicative functional for our example



If you run :ref:`the code that first simulated that example <addfunc_egcode>` again and then the method call



.. code-block:: python3

    amf.plot_multiplicative(T)
    plt.show()



As before, when we plotted multiple realizations of a component in the 2nd, 3rd, and 4th panels, we also plotted population 95% confidence bands computed using the LinearStateSpace class

Comparing this figure and the last also helps show how geometric growth differs from
arithmetic growth 


A peculiar large sample property
---------------------------------

Hansen and Sargent :cite:`Hans_Sarg_book_2016` (ch. 6) note that the martingale component
:math:`\widetilde M_t` of the multiplicative decomposition has a peculiar property

*  While :math:`E_0 \widetilde M_t = 1` for all :math:`t \geq 0`,
   nevertheless :math:`\ldots`

*  As :math:`t \rightarrow +\infty`, :math:`\widetilde M_t` converges to
   zero almost surely

The following simulation of many paths of :math:`\widetilde M_t` illustrates this property



.. code-block:: python3

    np.random.seed(10021987)
    amf.plot_martingales(12000)
    plt.show()





