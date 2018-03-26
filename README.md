# elezioni2018-pivot-scrutini

		<div>
			<div
				<div>
					<textarea id="output" type="text"  name="txtarea" style="font-family: Arial;font-size: 12pt;width:100%;height:500px;overflow:scroll">
					</textarea>
				</div>
			</div>
			<div>
				<div>
					<button onclick="saveAsCSV();">
						<span style="font-size:0.8em;vertical-align:10%">download csv</span>
					</button>
				</div>
			</div>
		</div>
		
		
			<script
  		src="https://code.jquery.com/jquery-3.3.1.min.js"
  		integrity="sha256-FgpCb/KJQlLNfOu91ta32o/NMZxltwRo8QtmkMRdAu8="
  		crossorigin="anonymous"></script>
				       
	<script src="https://cdn.rawgit.com/gjrichter/data.js/1.11/data.js"></script>

	<script type="text/javascript">

	// --------------------------------------------------------------------------------------------
	// map multiple 'coleggi eletorali' of metropolitan cities to unique id's (pro_com ISTAT)   
	// --------------------------------------------------------------------------------------------

	var procomMapA = [];
		procomMapA["TORINO - PIEMONTE 1 - 01 - 01"] = 1272;
		procomMapA["TORINO - PIEMONTE 1 - 01 - 02"] = 1272;
		procomMapA["TORINO - PIEMONTE 1 - 01 - 03"] = 1272;
		procomMapA["TORINO - PIEMONTE 1 - 01 - 04"] = 1272;
		procomMapA["MILANO - LOMBARDIA 1 - 02 - 09"] = 15146;
		procomMapA["MILANO - LOMBARDIA 1 - 03 - 08"] = 15146;
		procomMapA["MILANO - LOMBARDIA 1 - 03 - 11"] = 15146;
		procomMapA["MILANO - LOMBARDIA 1 - 03 - 12"] = 15146;
		procomMapA["MILANO - LOMBARDIA 1 - 03 - 13"] = 15146;
		procomMapA["MILANO - LOMBARDIA 1 - 03 - 14"] = 15146;
		procomMapA["GENOVA - LIGURIA - 01 - 03"] = 10025;
		procomMapA["GENOVA - LIGURIA - 02 - 04"] = 10025;
		procomMapA["GENOVA - LIGURIA - 02 - 05"] = 10025;
		procomMapA["BOLOGNA - EMILIA-ROMAGNA - 03 - 06"] = 37006;
		procomMapA["BOLOGNA - EMILIA-ROMAGNA - 03 - 07"] = 37006;
		procomMapA["FIRENZE - TOSCANA - 03 - 01"] = 48017;
		procomMapA["FIRENZE - TOSCANA - 03 - 02"] = 48017;
		procomMapA["ROMA - LAZIO 1 - 01 - 01"] = 58091;
		procomMapA["ROMA - LAZIO 1 - 01 - 02"] = 58091;
		procomMapA["ROMA - LAZIO 1 - 01 - 03"] = 58091;
		procomMapA["ROMA - LAZIO 1 - 01 - 04"] = 58091;
		procomMapA["ROMA - LAZIO 1 - 01 - 05"] = 58091;
		procomMapA["ROMA - LAZIO 1 - 01 - 06"] = 58091;
		procomMapA["ROMA - LAZIO 1 - 01 - 08"] = 58091;
		procomMapA["ROMA - LAZIO 1 - 01 - 09"] = 58091;
		procomMapA["ROMA - LAZIO 1 - 01 - 10"] = 58091;
		procomMapA["ROMA - LAZIO 1 - 01 - 11"] = 58091;
		procomMapA["ROMA - LAZIO 1 - 02 - 06"] = 58091;
		procomMapA["ROMA - LAZIO 1 - 02 - 08"] = 58091;
		procomMapA["ROMA - LAZIO 1 - 02 - 09"] = 58091;
		procomMapA["ROMA - LAZIO 1 - 02 - 10"] = 58091;
		procomMapA["ROMA - LAZIO 1 - 02 - 11"] = 58091;
		procomMapA["ROMA - LAZIO 1 - 03 - 07"] = 58091;
		procomMapA["NAPOLI - CAMPANIA 1 - 02 - 05"] = 63049;
		procomMapA["NAPOLI - CAMPANIA 1 - 02 - 06"] = 63049;
		procomMapA["NAPOLI - CAMPANIA 1 - 02 - 07"] = 63049;
		procomMapA["NAPOLI - CAMPANIA 1 - 02 - 08"] = 63049;
		procomMapA["BARI - PUGLIA - 01 - 01"] = 72006;
		procomMapA["BARI - PUGLIA - 01 - 02"] = 72006;
		procomMapA["PALERMO - SICILIA 1 - 01 - 01"] = 82053;
		procomMapA["PALERMO - SICILIA 1 - 01 - 02"] = 82053;
		procomMapA["PALERMO - SICILIA 1 - 01 - 03"] = 82053;

	// --------------------------------------------------------------------------------------------
	// stringify table object to csv   
	// --------------------------------------------------------------------------------------------

	var dumpTable = function(tableObj){

		var numRows = tableObj.records.length;
		var numCols = tableObj.fields.length;
		var records = tableObj.records;
		
		var columns = [];
		for ( i=0; i<numCols; i++ ){
			columns.push( {"title": tableObj.fields[i].id} );			
		}

		var szText = "";
		for ( i in columns ){
			szText += (szText.length?";":"") + columns[i].title;	
		}
		szText += "\r\n";
		for ( i in records ){
			var szRow = "";
			for ( ii in records[i] ){
				szRow += (szRow.length?";":"") + records[i][ii];
			}
			szText += szRow;	
			szText += "\r\n";
		}

		return szText;
	};

	// --------------------------------------------------------------------------------------------
	// save content of HTML text area id="oputput" to local filesystem
	// --------------------------------------------------------------------------------------------

	var saveAsCSV = function()
	{
		var textToWrite = $("#output").html();
		var textFileAsBlob = new Blob([textToWrite], {type:'text/plain'});
		var fileNameToSaveAs = "test.csv";

		var downloadLink = document.createElement("a");
		downloadLink.download = fileNameToSaveAs;
		downloadLink.innerHTML = "Download File";
		if (window.URL != null)
		{
			// Chrome allows the link to be clicked
			// without actually adding it to the DOM.
			downloadLink.href = window.URL.createObjectURL(textFileAsBlob);
		}
		else
		{
			// Firefox requires the link to be added to the DOM
			// before it can be clicked.
			downloadLink.href = window.URL.createObjectURL(textFileAsBlob);
			downloadLink.onclick = destroyClickedElement;
			downloadLink.style.display = "none";
			document.body.appendChild(downloadLink);
		}

		downloadLink.click();
	};

	// --------------------------------------------------------------------------------------------
	// extensions to the Data object (defined by data.js)
	// --------------------------------------------------------------------------------------------

	Data.Table.prototype.lookupArray = function(szValue,szLookup){
		var lookupA = [];
		var idA = this.column(szLookup).values();		
		var valueA = this.column(szValue).values();
		for ( i in idA ){
			lookupA[String(idA[i])] = valueA[i]||"-";
		}
		return lookupA;
	}

	Data.Table.prototype.lookup = function(value,option){
		var colValue = option.value;
		var colLookup = option.lookup;
		var sCacheId = colValue+"_"+colLookup;
		if ( !(this.lookupsA && this.lookupsA[sCacheId]) )	{
			this.lookupsA = this.lookupsA || [];
			this.lookupsA[sCacheId] = this.lookupArray(colValue,colLookup);
		}
		return (this.lookupsA[sCacheId][value]||"-");
	}

	// --------------------------------------------------------------------------------------------
	// here we go
	// --------------------------------------------------------------------------------------------

	$(document).ready(function() {

		$("#output").html("");
		$("#output").append("using data.js "+Data.version);
		$("#output").append("\nloading data ...");
		$("#output").append("\nhttps://raw.githubusercontent.com/ondata/elezionipolitiche2018/master/dati/scrutiniCI_cm.csv");
		$("#output").append("\nhttps://raw.githubusercontent.com/ondata/elezionipolitiche2018/master/risorse/comuniViminaleISTAT.csv");
		$("#output").append("\nhttps://raw.githubusercontent.com/ondata/elezionipolitiche2018/master/dati/camera_geopolitico_italia.csv");
		$("#output").append("\nand make pivot table ...");

		// --------------------------------------------------------------------------------------------
		// get multiple data sources and process 
		// --------------------------------------------------------------------------------------------

		var broker = new Data.Broker()
			.addSource("https://raw.githubusercontent.com/ondata/elezionipolitiche2018/master/dati/scrutiniCI_cm.csv","csv")
			.addSource("https://raw.githubusercontent.com/ondata/elezionipolitiche2018/master/risorse/comuniViminaleISTAT.csv","csv")
			.addSource("https://raw.githubusercontent.com/ondata/elezionipolitiche2018/master/dati/camera_geopolitico_italia.csv","csv")
			.realize(
		
		function(broker) {

			var scrutini					= broker.sourceQueryA[0].data;
			var comuniViminaleISTAT			= broker.sourceQueryA[1].data;
			var camera_geopolitico_italia	= broker.sourceQueryA[2].data;

			// --------------------------------------------------------------------------------------------
			// select rows with tipo_riga == 'LI'   
			// --------------------------------------------------------------------------------------------

			scrutini = scrutini.select("WHERE tipo_riga == LI");

			// --------------------------------------------------------------------------------------------
			// create new column 'pro_com' from ELIGENDO_C_UID_CI (code from Ministero dell'Interno)
			//	for metropolia cities there exists more than one code and the 'names' in the id => name 
			//	lookup table 'camera_geopolitico_italia.csv' are like 'TORINO - PIEMONTE 1 - 01 - 01'
			//  i created a list (procomMapA) to map these sub city names onto the cities ISTAT id (pro_com) 
			// --------------------------------------------------------------------------------------------

			// create assoc.array with ELIGENDO_C_NOME => PRO_COM from comuniViminaleISTAT.csv
			var procomA = comuniViminaleISTAT.lookupArray("PRO_COM","ELIGENDO_C_UID_CI");

			// create assoc.array with id ==> nome from camera_geopolitico_italia.csv (id == ELIGENDO_C_UID_CI)
			var nomeA = camera_geopolitico_italia.lookupArray("nome","id");

			// create new column
			scrutini.addColumn({source:'codice',destination:'pro_com'},
				function(value){
					if ( typeof(procomA[value]) != "undefined" ){
						return (procomA[value]);
					}else{
						// codice not found in comuniViminaleISTAT.csv (città metropolitane)
						// then map to name and lookup in my list procomMapA[]
						return procomMapA[nomeA[value]];
					}
			});

			// --------------------------------------------------------------------------------------------
			// create new column 'nome' from 'camera_geopolitico_italia.csv'
			// --------------------------------------------------------------------------------------------

			scrutini.addColumn({source:'codice',destination:'nome'},
				function(value){
					return (camera_geopolitico_italia.lookup(value,{value:"nome",lookup:"id"}));
			});

			// --------------------------------------------------------------------------------------------
			// create new column 'nome_lower' from 'comuniViminaleISTAT.csv'
			// --------------------------------------------------------------------------------------------

			scrutini.addColumn({source:'pro_com',destination:'nome_lower'},
				function(value){
					return (comuniViminaleISTAT.lookup(value,{value:"COMUNE",lookup:"PRO_COM"}));
			});

			// --------------------------------------------------------------------------------------------
			// make pivot table with columns == descr_lista (partiti)  
			// --------------------------------------------------------------------------------------------

			var pivot = scrutini.pivot({
							lead:	'codice',
							keep:	['pro_com','nome','nome_lower'],
							cols:	'descr_lista',
							value:  "voti" 
						  });

			// --------------------------------------------------------------------------------------------
			// create new columns with values of 'coalizioni'   
			// --------------------------------------------------------------------------------------------

			// 'centro sinistra'   
			// --------------------------------------------------------------------------------------------
			var i1 = pivot.columnIndex("PARTITO DEMOCRATICO");
			var i2 = pivot.columnIndex("+EUROPA");
			var i3 = pivot.columnIndex("ITALIA EUROPA INSIEME");
			var i4 = pivot.columnIndex("CIVICA POPOLARE LORENZIN");
			var i5 = pivot.columnIndex("SVP - PATT");
			pivot.addColumn({destination:'CSIN'},
				function(value){
					return (value[i1]+value[i2]+value[i3]+value[i4]+value[i5]);
			});
			// 'centro sinistra + liberi e uguali'   
			// --------------------------------------------------------------------------------------------
			var i1 = pivot.columnIndex("PARTITO DEMOCRATICO");
			var i2 = pivot.columnIndex("+EUROPA");
			var i3 = pivot.columnIndex("ITALIA EUROPA INSIEME");
			var i4 = pivot.columnIndex("CIVICA POPOLARE LORENZIN");
			var i5 = pivot.columnIndex("SVP - PATT");
			var i6 = pivot.columnIndex("LIBERI E UGUALI");
			pivot.addColumn({destination:'CSINPIU'},
				function(value){
					return (value[i1]+value[i2]+value[i3]+value[i4]+value[i5]+value[i6]);
			});
			// centro destra'   
			// --------------------------------------------------------------------------------------------
			var i1 = pivot.columnIndex("FORZA ITALIA");
			var i2 = pivot.columnIndex("LEGA");
			var i3 = pivot.columnIndex("FRATELLI D'ITALIA CON GIORGIA MELONI");
			var i4 = pivot.columnIndex("NOI CON L'ITALIA - UDC");
			pivot.addColumn({destination:'CDES'},
				function(value){
					return (value[i1]+value[i2]+value[i3]+value[i4]);
			});

			// --------------------------------------------------------------------------------------------
			// aggregate rows with same value of column 'pro_com' 
			// so we create the sum values for the metropolitan cities (ROMA, MILANO, ...)
			// --------------------------------------------------------------------------------------------

			pivot.condense('pro_com',{keep:'codice'});

			// --------------------------------------------------------------------------------------------
			// show final table   
			// --------------------------------------------------------------------------------------------

			$("#output").append("print data ...");
			$("#output").html(dumpTable(pivot));

		});	// end Data.Broker

	}); // end $(document).ready

	</script>


