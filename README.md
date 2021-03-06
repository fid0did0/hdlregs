About HDLRegs
=============

HDLRegs is an open-source HDL register file generator written in the Python programming language. It takes a register specification in JSON format, 
and generates the following output files:

  * VHDL package
  * Synthesizable VHDL component
  * C header
  * HTML documentation

Register Specification
======================

The register structure for a given module (e.g. UART, timer etc.) is specified in a text-file formatted according to the JavaScript Object Notation (JSON).
Here is an example register specification showcasing a few capabilities of HDLRegs:

    {
      "name"        : "example",
      "description" : "An example module demonstrating the capabilities of HDLRegs",
      "width"       : 32,
      "registers":
        [
            {
              "name"          : "version",
              "description"   : "Version register implemented using read-only fields",
              "access"        : "read-only",
              "fields"        :
                  [
                    {
                        "name"        : "high",
                        "description" : "high-byte of the version number",
                        "bitWidth"    : 8,
                        "reset"       : 1
                    },                            
                    {
                        "name"        : "low",
                        "description" : "low-byte of the version number",
                        "bitWidth"    : 8,
                        "reset"       : 0                        
                    }
                  ]                        
            },
            
            {
              "name"          : "control",
              "description"   : "Control register with a mix of read/write, write-only and read-only fields",
              "access"        : "read-write",
              "addressOffset" : "0x100",
              "reset"         : 0,
              "fields"        :
                  [
                    {
                        "name"        : "reset",
                        "description" : "reset the module",
                        "bitOffset"   : 31,
                        "bitWidth"    : 1,
                        "reset"       : 1
                    },
                    {
                        "name"        : "done",
                        "description" : "signals that the processing has completed",
                        "bitWidth"    : 1,
                        "access"      : "read-only"
                    },                                      
                    {
                        "name"        : "start",
                        "description" : "start the processing",
                        "bitWidth"    : 1,
                        "access"      : "write-only",
                        "bitOffset"   : 0,
                        "selfClear"   : true
                    }                  
                  ]          
            }
        ]
    }    

You can have a look at the generated files in the [example/output](https://github.com/noasic/hdlregs/tree/master/example/output) directory.

Usage
=====

To generate the output files from the JSON specification, execute the hdlregs.py script with the path to the JSON register specification as an argument. e.g.:
    
    python hdlregs.py example/example.json

Compatibility
=============

The VHDL register file component generated by HDLRegs has a generic host processor interface, which consists of the following ports:

    clk     : in  std_logic;                     -- system clock
    rst     : in  std_logic;                     -- synchronous, high-active
    addr    : in  std_logic_vector(31 downto 0); -- read/write address
    cs      : in  std_logic;                     -- chip select
    rnw     : in  std_logic;                     -- read (1) or write (0)
    datain  : in  std_logic_vector(31 downto 0); -- write data
    dataout : out std_logic_vector(31 downto 0); -- read data
    
VHDL adapter components are provided for connecting an HDLRegs-generated register file to Xilinx IPIF and AXI4-Lite (coming soon) interfaces.

Limitations
===========

HDLRegs development started in the summer of 2013, and although it has already been used with great success in a commercial project, I still consider it work in progress.

The most important limitation is that it currently supports only 32-bit wide registers files.

Support
=======

If you need help with HDLRegs, or if you'd like to request new features (such as clear-on-read bit fields), please start a topic in the HDLRegs category on [FPGA Exchange](http://fpga-exchange.com/category/hdlregs).

License
=======

HDLRegs is released under the terms of the "FreeBSD License", a permissive free software license:

    Copyright (c) 2013, Guy Eschemann
    All rights reserved.
    
    Redistribution and use in source and binary forms, with or without
    modification, are permitted provided that the following conditions are met: 
    
    1. Redistributions of source code must retain the above copyright notice, this
       list of conditions and the following disclaimer. 
    2. Redistributions in binary form must reproduce the above copyright notice,
       this list of conditions and the following disclaimer in the documentation
       and/or other materials provided with the distribution. 
    
    THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
    ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
    WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
    DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR
    ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
    (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
    LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND
    ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
    (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
    SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
    
    The views and conclusions contained in the software and documentation are those
    of the authors and should not be interpreted as representing official policies, 
    either expressed or implied, of the FreeBSD Project.
