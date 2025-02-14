# ECE 281 ICE 3: Ripple-Carry Adder with Top Level Design

This is a **template** repository.

[ICE 3 instructions](https://usafa-ece.github.io/ece281-book/ICE/ICE3.html)

Targeted toward Digilent Basys3. Make sure to install the [board files](https://github.com/Xilinx/XilinxBoardStore/tree/2018.2/boards/Digilent/basys3).

Tested on Vivado 2024.2

---

## GitHub Actions Testbench

The workflow uses the [setup-ghdl-ci](https://github.com/ghdl/setup-ghdl-ci) GitHub action
to run a *nightly* build of [GHDL](https://ghdl.github.io/ghdl/).

First, the workflow uses GHDL to **analyze** all `.vhd` files in `src/`.

Then it **elaborates** the entity defined by `$TB_ENTITY`

Finally, the workflow **runs** the simulation. If successful then it will quietly exit with a `0` code.
If any of the `assert` statements fail then GHDL will cease the simulation and exit with non-zero code; this will also cause the workflow to fail.
Assert statements of other severity levels will be reported, but not fail the workflow.
Documentation Statement:
I used ChatGPT to generate these lines of code:
architecture top_basys3_arch of top_basys3 is 

	-- Declare the Ripple-Carry Adder component
	component ripple_adder
		port(
			A     : in  std_logic_vector(3 downto 0);
			B     : in  std_logic_vector(3 downto 0);
			Cin   : in  std_logic;
			S     : out std_logic_vector(3 downto 0);
			Cout  : out std_logic
		);
	end component;

	-- Internal Signals for Connections
	signal w_A     : std_logic_vector(3 downto 0);
	signal w_B     : std_logic_vector(3 downto 0);
	signal w_Cin   : std_logic;
	signal w_Sum   : std_logic_vector(3 downto 0);
	signal w_Cout  : std_logic;

begin

	-- Extract Inputs from Switches
	w_A   <= sw(4 downto 1);      -- A comes from switches 4-1
	w_B   <= sw(15 downto 12);    -- B comes from switches 15-12
	w_Cin <= sw(0);               -- Cin comes from switch 0

	-- Instantiate the Ripple-Carry Adder
	u1: ripple_adder
	port map(
		A     => w_A,
		B     => w_B,
		Cin   => w_Cin,
		S     => w_Sum,
		Cout  => w_Cout
	);

	-- Assign Outputs to LEDs
	led(3 downto 0)  <= w_Sum;   -- Display sum on LED 3-0
	led(15)          <= w_Cout;  -- Display carry-out on LED 15
	led(14 downto 4) <= (others => '0'); -- Ground unused LEDs

end top_basys3_arch;
