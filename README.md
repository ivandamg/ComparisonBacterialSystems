# ComparisonBacterialSystems

Analysis of different bacterials systems like the Type VI secretion system. Between two strains. 


1. Identify the complete gene list of the system.

2. Blast those genes in two strains.

        # make db
        a=0;for i in $(ls *.fna); do echo $(echo $i | cut -d'_' -f1,2,3) ;makeblastdb -dbtype nucl -in $i -parse_seqids -out db_genomes/$(echo $i | cut -d'_' -f1)_db ; done

        # blast sequences
        a=0;for i in $(ls *.fna); do echo $(echo $i | cut -d'_' -f1) ;tblastn -db db_genomes/$(echo $i | cut -d'_' -f1)_db -outfmt 6 -evalue 1e-6 -show_gis -num_alignments 2 -max_hsps 2 -num_threads 30 -out db_genomes/blast_Aux1Cluster_$(echo $i | cut -d'_' -f1).xml -query ~/Documents/Melanie/TypeVISS_O395_A1552/REF_TypeVISequences/Aux1Cluster_TypeVISS_N16961.faa ; done

        a=0;for i in $(ls *.fna); do echo $(echo $i | cut -d'_' -f1) ;tblastn -db db_genomes/$(echo $i | cut -d'_' -f1)_db -outfmt 6 -evalue 1e-6 -show_gis -num_alignments 2 -max_hsps 3 -num_threads 30 -out db_genomes/blast_Aux2Cluster_$(echo $i | cut -d'_' -f1).xml -query ~/Documents/Melanie/TypeVISS_O395_A1552/REF_TypeVISequences/Aux2Cluster_TypeVISS_N16961.faa ; done

3. Extract coordinates of the genes in the genome assemblies

        # Then extract coordinates to make fasta files
        for i in $(ls blast_Aux*.xml); do echo $i ; star="$(cat $i | cut -f2 |sed 's/^/blastdbcmd /g' | sed 's/ / -entry /g' | awk '$3="\x27"$3"\x27"')"; range="$(cat $i | cut -f9,10 | while read line; do echo $line | sed 's/ /\n/g' | sort | gawk '{line=line " " $0} END {print line}' ; done | sed 's/^ /-range/g' | sed 's/ /-/' | sed 's/range/range /')" ; lines="$(cat $i | wc -l)"; export lines # put variable in open environment ;; db="$(echo $i | cut -d'_' -f1 | sed 's/\.xml/_db/g' | sed 's/^/-db /' | perl -ne 'print $_ x $ENV{lines}')";  nam1="$(cat $i | cut -f1  | cut -f1 -d'/')"; nam2="$(echo $i | cut -d'_' -f1 | sed 's/\.xml//g' | perl -ne 'print $_ x $ENV{lines}')"; end="$(paste <(echo "$nam1") <(echo "$nam2") --delimiters '_' | sed 's/^/> Seq_/'| sed 's/$/.fa/' )"; paste <(echo "$star") <(echo "$db") <(echo "$range") <(echo "$end") --delimiters ' '; done


4. Revise presence of all sequences and curate manually the coordinates and size of the genes.

