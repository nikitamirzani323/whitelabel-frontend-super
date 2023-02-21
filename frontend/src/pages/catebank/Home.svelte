<script>
    import { Input } from "sveltestrap";
    
    import Panel from "../../components/Panel.svelte";
    import Loader from "../../components/Loader.svelte";
	import Button from "../../components/Button.svelte";
	import Modal from "../../components/Modal.svelte";
    import { createEventDispatcher } from "svelte";

    
	export let table_header_font = ""
	export let table_body_font = ""
	export let token = ""
	export let listHome = []
	export let totalrecord = 0
    let dispatch = createEventDispatcher();
	let title_page = "CATEGORY BANK"
    let sData = "";
    let myModal_newentry = "";
    let curr_flag = true;
    let catebank_field = "";
    let status_field = "";
    let create_field = "";
    let update_field = "";

    let catebank_id = 0;
    let catebank_name = "";
    let listbanktype = [];

    let idrecord = "";
    let searchCatebank = "";
    let filterCatebank = [];
    let searchBanktype = "";
    let filterBanktype = [];
    let css_loader = "display: none;";
    let msgloader = "";

    $: {
        if (searchCatebank) {
            filterCatebank = listHome.filter(
                (item) =>
                    item.home_name
                        .toLowerCase()
                        .includes(searchCatebank.toLowerCase())
            );
        } else {
            filterCatebank = [...listHome];
        }
    }
    
    const NewData = (e,id,catebank,status,create,update) => {
        sData = e
        if(sData == "New"){
            clearField()
        }else{
            curr_flag = true;
            idrecord = id
            catebank_field = catebank;
            status_field = status;
            create_field = create;
            update_field = update;
        }
        myModal_newentry = new bootstrap.Modal(document.getElementById("modalentrycrud"));
        myModal_newentry.show();
        
    };
    const ShowNewBankType = (e,idgame,prediksigame,nmgame,imggame,statusgame,creategame,updategame) => {
        sDataPrediksi = e
        if(e == "Edit"){
            prediksislot_id_field = idgame
            prediksislot_prediksi_field = prediksigame
            prediksislot_name_field = nmgame
            prediksislot_image_field = imggame
            prediksislot_status_field = statusgame
            prediksislot_create_field = creategame
            prediksislot_update_field = updategame
        }else{
            clearFieldGame();
        }
        myModal_newentry = new bootstrap.Modal(document.getElementById("modalentrygame"));
        myModal_newentry.show();
    };
    const ShowBankType = (id,name) => {
        // clearField()
        call_listbanktype(id)
     
        catebank_id = id
        catebank_name = name
        myModal_newentry = new bootstrap.Modal(document.getElementById("modallistbanktype"));
        myModal_newentry.show();
    };
    const RefreshHalaman = () => {
        dispatch("handleRefreshData", "call");
    };
    
    async function call_listbanktype(e) {
        listbanktype = [];
        css_loader = "display: inline-block;";
        msgloader = "Fetching...";
        const res = await fetch("/api/banktype", {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
                Authorization: "Bearer " + token,
            },
            body: JSON.stringify({
                page: "KELUARAN-view",
                providerslot_id: e,
            }),
        });
        const json = await res.json();
        if (json.status == 200) {
            let record = json.record;
            if (record != null) {
                let prediksi_statuscss = "";
                let prediksi_css = "";
                let prediksi_css_color = "";
                let prediksi_class = "";
                for (var i = 0; i < record.length; i++) {
                    if(record[i]["prediksislot_status"] == "Y"){
                        prediksi_statuscss = "background:#FFEB3B;font-weight:bold;color:black;"
                    }else{
                        prediksi_statuscss = "background:#E91E63;font-weight:bold;color:white;"
                    }
                    if(parseInt(record[i]["prediksislot_prediksi"]) > 9 && parseInt(record[i]["prediksislot_prediksi"]) < 50){
                        prediksi_css = "color: #dc3545;"
                        prediksi_css_color = "color: white;"
                        prediksi_class = "bg-danger"
                    }
                    if(parseInt(record[i]["prediksislot_prediksi"]) > 49 && parseInt(record[i]["prediksislot_prediksi"]) < 71){
                        prediksi_css = "color: #ffc107;"
                        prediksi_css_color = "color: black;"
                        prediksi_class = "bg-warning"
                    }
                    if(parseInt(record[i]["prediksislot_prediksi"]) > 70 && parseInt(record[i]["prediksislot_prediksi"]) < 101){
                        prediksi_css = "color: #28a745;"
                        prediksi_css_color = "color: white;"
                        prediksi_class = "bg-success"
                    }
                    listbanktype = [
                        ...listbanktype,
                        {
                            prediksislot_id: record[i]["prediksislot_id"],
                            prediksislot_nmprovider: record[i]["prediksislot_nmprovider"],
                            prediksislot_name: record[i]["prediksislot_name"],
                            prediksislot_prediksi: record[i]["prediksislot_prediksi"],
                            prediksislot_prediksicss: prediksi_css,
                            prediksislot_prediksicss_color: prediksi_css_color,
                            prediksislot_prediksiclass: prediksi_class,
                            prediksislot_image: record[i]["prediksislot_image"],
                            prediksislot_status: record[i]["prediksislot_status"],
                            prediksislot_statuscss: prediksi_statuscss,
                            prediksislot_create: record[i]["prediksislot_create"],
                            prediksislot_update: record[i]["prediksislot_update"],
                        },
                    ];
                }
            }
        }
        msgloader = json.message;
        setTimeout(function () {
            css_loader = "display: none;";
        }, 1000);
    }
    async function handleSave() {
        let flag = true
        let msg = ""
        if(sData == "New"){
            if(idrecord == ""){
                flag = false
                msg += "The ID is required\n"
            }
            if(catebank_field == ""){
                flag = false
                msg += "The Category Bank is required\n"
            }
        }else{
            if(idrecord == ""){
                flag = false
                msg += "The ID is required\n"
            }
            if(catebank_field == ""){
                flag = false
                msg += "The Category bank is required\n"
            }
        }
        
        if(flag){
            css_loader = "display: inline-block;";
            msgloader = "Sending...";
            const res = await fetch("/api/catebanksave", {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                    Authorization: "Bearer " + token,
                },
                body: JSON.stringify({
                    sdata: sData,
                    page:"CURRENCY-SAVE",
                    catebank_id: idrecord,
                    catebank_name: catebank_field,
                    catebank_status: status_field,
                }),
            });
            const json = await res.json();
            if (json.status == 200) {
                if(sData=="New"){
                    clearField()
                }
                msgloader = json.message;
                RefreshHalaman()
            } else if(json.status == 403){
                alert(json.message)
            } else {
                msgloader = json.message;
            }
            setTimeout(function () {
                css_loader = "display: none;";
            }, 1000);
        }else{
            alert(msg)
        }
    }
    
    function clearField(){
        curr_flag = false;
        idrecord = "";
        catebank_field = "";
        status_field = "";
        create_field = "";
        update_field = "";
    }
    function callFunction(event){
        switch(event.detail){
            case "NEW":
                NewData("New","","","");
                break;
            case "REFRESH":
                RefreshHalaman();break;
            case "SAVE":
                handleSubmit();break;
            case "NEW_BANKTYPE":
                ShowNewBankType("New",0,0,"","","");
        }
    }
    const handleKeyboard_checkenter = (e) => {
        let keyCode = e.which || e.keyCode;
        if (keyCode === 13) {
                filterTafsirMimpi = [];
                listHome = [];
                const tafsir = {
                    searchTafsirMimpi,
                };
                dispatch("handleTafsirMimpi", tafsir);
        }  
    };
    function status(e){
        let result = "HIDE"
        if(e == "Y"){
            result = "SHOW"
        }
        return result
    }
</script>
<div id="loader" style="margin-left:50%;{css_loader}">
    {msgloader}
</div>
<div class="container" style="margin-top: 70px;">
    <div class="row">
        <div class="col-sm-12">
            <Button
                on:click={callFunction}
                button_function="NEW"
                button_title="New"
                button_css="btn-dark"/>
            <Button
                on:click={callFunction}
                button_function="REFRESH"
                button_title="Refresh"
                button_css="btn-primary"/>
            <Panel
                card_search={true}
                card_title="{title_page}"
                card_footer={totalrecord}>
                <slot:template slot="card-search">
                    <div class="col-lg-12" style="padding: 5px;">
                        <input
                            bind:value={searchCatebank}
                            on:keypress={handleKeyboard_checkenter}
                            type="text"
                            class="form-control"
                            placeholder="Search Category Bank"
                            aria-label="Search"/>
                    </div>
                </slot:template>
                <slot:template slot="card-body">
                    <table class="table table-striped ">
                        <thead>
                            <tr>
                                <th NOWRAP width="1%" style="text-align: center;vertical-align: top;" colspan="2">&nbsp;</th>
                                <th NOWRAP width="1%" style="text-align: center;vertical-align: top;font-weight:bold;font-size:{table_header_font};">NO</th>
                                <th NOWRAP width="1%" style="text-align: center;vertical-align: top;font-weight:bold;font-size:{table_header_font};">STATUS</th>
                                <th NOWRAP width="*" style="text-align: left;vertical-align: top;font-weight:bold;font-size: {table_header_font};">CATEGORY BANK</th>
                                <th NOWRAP width="10%" style="text-align: left;vertical-align: top;font-weight:bold;font-size: {table_header_font};">CREATE</th>
                                <th NOWRAP width="10%" style="text-align: left;vertical-align: top;font-weight:bold;font-size: {table_header_font};">UPDATE</th>
                            </tr>
                        </thead>
                        {#if totalrecord > 0}
                        <tbody>
                            {#each filterCatebank as rec }
                                <tr>
                                    <td NOWRAP style="text-align: center;vertical-align: top;cursor:pointer;">
                                        <i on:click={() => {
                                                NewData("Edit",
                                                rec.home_id,rec.home_name,rec.home_status,
                                                rec.home_create,rec.home_update);
                                            }} 
                                            class="bi bi-pencil"></i>
                                    </td>
                                    <td NOWRAP style="text-align: center;vertical-align: top;cursor:pointer;">
                                        <i on:click={() => {
                                                ShowBankType(rec.home_id,rec.home_name);
                                            }} 
                                            class="bi bi-file-earmark-plus"></i>
                                    </td>
                                    <td NOWRAP style="text-align: center;vertical-align: top;font-size: {table_body_font};">{rec.home_no}</td>
                                    <td NOWRAP style="text-align: center;vertical-align: top;font-size: {table_body_font};">
                                        <span style="padding: 5px;border-radius: 10px;padding-right:10px;padding-left:10px;{rec.home_statuscss}">
                                            {status(rec.home_status)}
                                        </span>
                                    </td>
                                    <td  style="text-align: left;vertical-align: top;font-size: {table_body_font};">{rec.home_name}</td>
                                    <td  NOWRAP style="text-align: left;vertical-align: top;font-size: {table_body_font};">{rec.home_create}</td>
                                    <td  NOWRAP style="text-align: left;vertical-align: top;font-size: {table_body_font};">{rec.home_update}</td>
                                </tr>
                            {/each}
                        </tbody>
                        {:else}
                        <tbody>
                            <tr>
                                <td colspan="20">
                                    <center>
                                        <Loader />
                                    </center>
                                </td>
                            </tr>
                        </tbody>
                        {/if} 
                    </table>
                </slot:template>
            </Panel>
        </div>
    </div>
</div>

<Modal
	modal_id="modalentrycrud"
	modal_size="modal-dialog-centered"
	modal_title="{title_page+"/"+sData}"
    modal_footer_css="padding:5px;"
	modal_footer={true}>
	<slot:template slot="body">
        <div class="mb-3">
            <label for="exampleForm" class="form-label">Category Bank</label>
            <Input
                bind:value={catebank_field}
                class="required"
                type="text"
                placeholder="Category Bank"/>
        </div>
        <div class="mb-3">
            <label for="exampleForm" class="form-label">Status</label>
            <select class="form-control required" bind:value="{status_field}">
                <option value="Y">SHOW</option>
                <option value="N">HIDE</option>
            </select>
        </div>
        {#if sData == "Edit"}
            <div class="alert alert-dark" role="alert" style="font-size:11px;padding:5px;">
                Create : {create_field}
                <br />
                Update : {update_field}
            </div>
        {/if}
	</slot:template>
	<slot:template slot="footer">
        <Button
            on:click={() => {
                handleSave();
            }} 
            button_function="SAVE"
            button_title="Save"
            button_css="btn-warning"/>
	</slot:template>
</Modal>

<Modal
  modal_id="modallistbanktype"
  modal_size="modal-dialog-centered modal-lg"
  modal_title="Category Bank : {catebank_name}"
  modal_body_css="padding:5px;margin:0px;height:500px;overflow-y: scroll; "
  modal_footer_css="padding:5px;"
  modal_search={true}
  modal_footer={true}>
  <slot:template slot="search">
    <input
        bind:value={searchBanktype}
        type="text"
        class="form-control"
        placeholder="Search Bank Type"
        aria-label="Search"/>
  </slot:template>
  <slot:template slot="body">
    <table class="table">
      <thead>
        <tr>
          <th NOWRAP width="1%" style="text-align: center;vertical-align: top;font-weight:bold;font-size:{table_header_font};" colspan=2>&nbsp;</th>
          <th NOWRAP width="1%" style="text-align: center;vertical-align: top;font-weight:bold;font-size:{table_header_font};">STATUS</th>
          <th NOWRAP width="20%" style="text-align: left;vertical-align: top;font-weight:bold;font-size:{table_header_font};">GAME</th>
          <th NOWRAP width="10%" style="text-align: center;vertical-align: top;font-weight:bold;font-size:{table_header_font};">IMAGE</th>
          <th NOWRAP width="*" style="text-align: right;vertical-align: top;font-weight:bold;font-size:{table_header_font};">PREDIKSI</th>
        </tr>
      </thead>
      <tbody>
        {#each filterBanktype as rec}
          <tr>
            <td NOWRAP style="text-align: center;vertical-align: top;cursor:pointer;">
              <i
                on:click={() => {
                  handleDeleteGameSlot(rec.prediksislot_id,rec.prediksislot_prediksi);
                }}
                class="bi bi-trash"/>
            </td>
            <td NOWRAP style="text-align: center;vertical-align: top;cursor:pointer;">
                <i
                  on:click={() => {
                    ShowNewGame("Edit",rec.prediksislot_id,rec.prediksislot_prediksi,rec.prediksislot_name,rec.prediksislot_image,rec.prediksislot_status,rec.prediksislot_create,rec.prediksislot_update);
                  }}
                  class="bi bi-pencil"/>
              </td>
            <td NOWRAP style="text-align: center;vertical-align: top;font-size: {table_body_font};{rec.prediksislot_statuscss}">{status(rec.prediksislot_status)}</td>
            <td NOWRAP style="text-align: left;vertical-align: top;font-size: {table_body_font};">{rec.prediksislot_name}</td>
            <td NOWRAP style="text-align: center;vertical-align: top;font-size: {table_body_font};">
                <img src="{rec.prediksislot_image}" width="40" alt="">
            </td>
            <td NOWRAP style="text-align: right;vertical-align: top;font-size: {table_body_font};">
                <div class="progress">
                    <div class="progress-bar progress-bar-striped {rec.prediksislot_prediksiclass}" role="progressbar" style="width: {rec.prediksislot_prediksi}%" aria-valuenow="{rec.prediksislot_prediksi}" aria-valuemin="0" aria-valuemax="100">
                        <span style="{rec.prediksislot_prediksicss_color}">{rec.prediksislot_prediksi}%</span>
                    </div>
                </div>
            </td>
          </tr>
        {/each}
      </tbody>
    </table>
  </slot:template>
  <slot:template slot="footer">
    <Button
      on:click={callFunction}
      button_function="NEW_BANKTYPE"
      button_title="New"
      button_css="btn-warning"/>
  </slot:template>
</Modal>

