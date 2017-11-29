Django Field Name	| Description	                  | Corresponding PostgreSQL Field Type
------------------|-------------------------------|------------------------------------
AutoField	        |auto-incrementing IntegerField	| 
BooleanField	    | true/false field (does not support null)	| 
CharField(max_length) |	String field	| 
DateField	        | date (represented as Python datetime.date instance)	| 
DecimalField(max_digits=<Int>, decimal_places=<Int>) |	fixed-precision decimal number, represented in Python as a Decimal instance	| 
DurationField	    | a field for storing periods of time, uses Python timedelta | interval
EmailField        |	a CharField with a built-in validator to check that the value has the syntax of an email address	| 
FloatField	      | A floating-point number represented by a Python float instance	| 
IntegerField	    | signed 32-bit integer	| 
NullBooleanField	| like BooleanField but allows null	| 
SlugField	        | like CharField but 50 char maxlength by default, only allows letters, numbers, underscores, hyphens	| 
TextField	        | A large text field	| 
TimeField	        | a time, represented in Python by a datetime.time instance	| 
URLField	        | a CharField for a URL	| 
ForeignKey(to, on_delete)	| defines a many-to-one relationship	| 
ManyToManyField(to)	| defines a many-to-many relationship	| 
OneToOneField(to)	| defines a one-to-one relationship	| 
