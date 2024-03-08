```
flowchart 
    subgraph d[Technique: Process Injection ]
        style d fill:#f1932d
        subgraph e[Sub-technique: Ptrace]
            style e fill:#f7f056
            subgraph a [Objective: Write Malicous Code to Another Process' Memory]
                style a fill:#90c987
                subgraph operation1 [Operation: Process Access]
                
                    PTRACE_ATTACH:::osApi
                end
                operation1:::operation
                subgraph operation2 [Operation: Register Save]
                    PTRACE_GETREGS:::osApi
                end
                operation2:::operation
                operation1-->operation2
                
                subgraph operation3 [Operation: Write Memory]
                    PTRACE_POKETEXT:::osApi
                end
                operation3:::operation
                operation2-->operation3
            end
            subgraph b [Objective: Execute Code]
                style b fill:#90c987
            
                subgraph operation4 [Operation: Set Register]
                    PTRACE_SETREGS:::osApi
                end
                operation4:::operation
                subgraph operation5 [Operation: Resume Execution]
                    PTRACE_CONT:::osApi
                    PTRACE_DETACH:::osApi
                end
                operation5:::operation
                operation4-->operation5
            end
            subgraph c [Objective: Resume execution to Original Process]
                style c fill:#90c987
                subgraph operation6 [Operation: Backup Memory]
                    PTRACE_PEEKTEXT:::osApi
                end
                operation6:::operation
                subgraph operation7 [Operation: Set Register]
                    x[PTRACE_SETREGS]:::osApi
                end
                operation7:::operation
                subgraph operation8 [Operation: Write Memory]
                    y[PTRACE_POKETEXT]:::osApi
                end
                operation8:::operation
                subgraph operation9 [Operation: Resume Execution]
                    z[PTRACE_DETACH]:::osApi
                end
                operation9:::operation
                operation6 ~~~ operation7
                PTRACE_CONT -.-> operation7
                operation7 -.-> operation8
                operation8 -.-> operation9
                operation6-.->operation8
            end
            operation3-->operation4
            operation2-.->operation6
        end
        click PTRACE_ATTACH "https://next.d3fend.mitre.org/dao/artifact/d3f:LinuxPtraceArgumentPTRACEATTACH/"
        click PTRACE_GETREGS "https://next.d3fend.mitre.org/dao/artifact/d3f:LinuxPtraceArgumentPTRACEGETREGS/"
        click PTRACE_POKETEXT "https://next.d3fend.mitre.org/dao/artifact/d3f:LinuxPtraceArgumentPTRACEPOKETEXT/"
        click PTRACE_SETREGS "https://next.d3fend.mitre.org/dao/artifact/d3f:LinuxPtraceArgumentPTRACESETREGS/"
        click PTRACE_CONT "https://next.d3fend.mitre.org/dao/artifact/d3f:LinuxPtraceArgumentPTRACECONT/"
        click PTRACE_DETACH "https://next.d3fend.mitre.org/dao/artifact/d3f:LinuxPtraceArgumentPTRACEDETACH/"
        click PTRACE_PEEKTEXT "https://next.d3fend.mitre.org/dao/artifact/d3f:LinuxPtraceArgumentPTRACEPEEKTEXT/"
        click x "https://next.d3fend.mitre.org/dao/artifact/d3f:LinuxPtraceArgumentPTRACESETREGS/"
        click y "https://next.d3fend.mitre.org/dao/artifact/d3f:LinuxPtraceArgumentPTRACEPOKETEXT/"
        click z "https://next.d3fend.mitre.org/dao/artifact/d3f:LinuxPtraceArgumentPTRACEDETACH/"
    end
    classDef operation fill:#7bafde;
    classDef osApi fill:#1965b0,color:#ffffff;
    classDef native fill:#ae76a3;
    classDef param fill:#FFA6A6,opacity:0.75,font-size:8pt;
    classDef highlevel fill:#09E72E,opacity:0.75,font-size:8pt;
    classDef access fill:#F6E8D6,opacity:0.75,font-size:8pt;
    classDef subgraphstyle margin-left:1cm
    class b subgraphstyle
```