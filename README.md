# delete Duplicate pagename 
use Getopt::Long;
my $option_vob;
my @option_path;
my $option_path;
print "Fix the same uri page\n";
&parseArgs;
@option_path=split(",",$option_vob);
print "\@option_path are :@option_path\n";
foreach $option_path(@option_path){
        if( ($option_path=~/\bemagent\b/) ||($option_path=~/\bemas\b/) ||($option_path=~/\bemcfw\b/) ||($option_path=~/\bemcore\b/) ||($option_path=~/\bemdb\b/) ||($option_path=~/\bemfa\b/) ||($option_path=~/\bemgc\b/) ||($option_path=~/\bemmos\b/) ||($option_path=~/\bempa\b/) ||($option_path=~/\bempp\b/)){
                $working_tld=$&;
                $working_tld=~s/[\s\n]//g;
                $tld=$option_path;
                $tld=~s/[\s\n]//g;
                `ade co -nc $ENV{SRCHOME}/$working_tld/test/src/config/navigation_config.xml`;   #check out file
                $check_file="$ENV{SRCHOME}/$working_tld/test/src/config/navigation_config.xml";     #extract pagename from "navigation_config.xml"
                open(PAGENAME,"$check_file")||die("Could not open file.");
                @wholeline=<PAGENAME>;
                foreach $f_line(@wholeline)
                {
                  if($f_line=~/page name=(".*")/)
                    {
                      push(@pagename,$f_line);
                    }
                }
                 foreach $pn (@pagename)                                         #read page one line by one line.
                {
                  chomp($pn);
                  #$pn=quotemeta("$pn");
                  my $count=`grep -c '$pn' $check_file`;                          #count repeat pagename
                  my @line=`grep -n '$pn' $check_file`;                           #real line exist same pagename
                  my $num=0;
                  my @subline=();
                  foreach $eachline(@line)                                        #list the line number of "pagename" appeared.
                {
                  my @spli=split(/:/,$eachline);
                  $subline[$num]=$spli[0];
                  $num++;
                }
                  @subline=reverse @subline;                             #reverse line number to make delete relate column convenient.
                  my $pagc=0;
                  my $NtoD=$count-1;

                  #print "\$count is $count...\$NtoD is $NtoD\n";
               while($pagc<($count-1)&&($NtoD>=1))
                  {
                   my $start=$subline[$pagc];
                   my $end=$subline[$pagc];
                   my $sign_end=`sed -n '$end p' $check_file`;
                   #print "\$sign_end is:$sign_end\n";
                   until ($sign_end=~/<\/page>/)
                    {
                      $end++;
                      $sign_end=`sed -n '$end p' $check_file`;
                    }
                  #print "\$start is $start...\$end is $end...\n";
                  `sed -i '$start,$end d' $check_file`;
                   $NtoD--;
                   $pagc++;
                }
             }
         }
  else
  {
    print "Have no such dir.";
      }
}
sub parseArgs{
die "incorrect usage" if (!GetOptions("d=s" => \$option_vob));
}
