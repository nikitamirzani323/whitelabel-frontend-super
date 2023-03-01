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
	let title_page = "COMPANY"
    let sData = "";
    let myModal_newentry = "";
    let idcompany_field = "";
    let idcompany_flag = true;
    let idcurr_field = "";
    let idcurr_flag = true;
    let nmcompany_field = "";
    let nmowner_field = "";
    let phoneowner_field = "";
    let emailowner_field = "";
    let status_field = "";
    let create_field = "";
    let update_field = "";
    
    let listcurr = [];
    let catebank_id = 0;
    let catebank_name = "";
    let listbanktype = [];
    let sDataBankType = "";
    let idbanktype_flag = true;
    let idbanktype_field = "";
    let namebanktype_field = "";
    let imgbanktype_field = "";
    let statusbanktype_field = "";
    let createbanktype_field = "";
    let updatebanktype_field = "";

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

        if (searchBanktype) {
            filterBanktype = listbanktype.filter(
                (item) =>
                    item.banktype_name
                        .toLowerCase()
                        .includes(searchBanktype.toLowerCase())
            );
        } else {
            filterBanktype = [...listbanktype];
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
    const ShowNewBankType = (e,idbanktype,nmbanktype,imgbanktype,statusbanktype,createbanktype,updatebanktype) => {
        
        sDataBankType = e
        if(e == "Edit"){
            sDataBankType = e;
            idbanktype_flag = true;
            idbanktype_field = idbanktype;
            namebanktype_field = nmbanktype;
            imgbanktype_field = imgbanktype;
            statusbanktype_field = statusbanktype;
            createbanktype_field = createbanktype;
            updatebanktype_field = updatebanktype;
        }else{
            clearFieldBankType();
        }
        myModal_newentry = new bootstrap.Modal(document.getElementById("modalcrudbanktype"));
        myModal_newentry.show();
    };
    const ShowBankType = (id,name) => {
        clearFieldBankType()
        call_listbanktype(id)
     
        catebank_id = id
        catebank_name = name
        myModal_newentry = new bootstrap.Modal(document.getElementById("modallistbanktype"));
        myModal_newentry.show();
    };
    const RefreshHalaman = () => {
        dispatch("handleRefreshData", "call");
    };
    async function call_listcurr(e) {
        listcurr = [];
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
                banktype_idcatebank: e,
            }),
        });
        const json = await res.json();
        if (json.status == 200) {
            let record = json.record;
            if (record != null) {
                let banktype_statuscss = "";
                for (var i = 0; i < record.length; i++) {
                    if(record[i]["banktype_status"] == "Y"){
                        banktype_statuscss = "background:#FFEB3B;font-weight:bold;color:black;"
                    }else{
                        banktype_statuscss = "background:#E91E63;font-weight:bold;color:white;"
                    }
                    listbanktype = [
                        ...listbanktype,
                        {
                            banktype_id: record[i]["banktype_id"],
                            banktype_name: record[i]["banktype_name"],
                            banktype_img: record[i]["banktype_img"],
                            banktype_status: record[i]["banktype_status"],
                            banktype_statuscss: banktype_statuscss,
                            banktype_create: record[i]["banktype_create"],
                            banktype_update: record[i]["banktype_update"],
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
                banktype_idcatebank: e,
            }),
        });
        const json = await res.json();
        if (json.status == 200) {
            let record = json.record;
            if (record != null) {
                let banktype_statuscss = "";
                for (var i = 0; i < record.length; i++) {
                    if(record[i]["banktype_status"] == "Y"){
                        banktype_statuscss = "background:#FFEB3B;font-weight:bold;color:black;"
                    }else{
                        banktype_statuscss = "background:#E91E63;font-weight:bold;color:white;"
                    }
                    listbanktype = [
                        ...listbanktype,
                        {
                            banktype_id: record[i]["banktype_id"],
                            banktype_name: record[i]["banktype_name"],
                            banktype_img: record[i]["banktype_img"],
                            banktype_status: record[i]["banktype_status"],
                            banktype_statuscss: banktype_statuscss,
                            banktype_create: record[i]["banktype_create"],
                            banktype_update: record[i]["banktype_update"],
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
    async function handleSaveBankType() {
        let flag = true
        let msg = ""
        if(sDataBankType == "New"){
            if(idbanktype_field == ""){
                flag = false
                msg += "The ID is required\n"
            }
            if(namebanktype_field == ""){
                flag = false
                msg += "The Name is required\n"
            }
            if(imgbanktype_field == ""){
                flag = false
                msg += "The Image is required\n"
            }
            if(statusbanktype_field == ""){
                flag = false
                msg += "The Status is required\n"
            }
        }else{
            if(idbanktype_field == ""){
                flag = false
                msg += "The ID is required\n"
            }
            if(namebanktype_field == ""){
                flag = false
                msg += "The Name is required\n"
            }
            if(imgbanktype_field == ""){
                flag = false
                msg += "The Image is required\n"
            }
            if(statusbanktype_field == ""){
                flag = false
                msg += "The Status is required\n"
            }
        }
        
        if(flag){
            css_loader = "display: inline-block;";
            msgloader = "Sending...";
            const res = await fetch("/api/banktypesave", {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                    Authorization: "Bearer " + token,
                },
                body: JSON.stringify({
                    sdata: sDataBankType,
                    page:"CURRENCY-SAVE",
                    banktype_id: idbanktype_field,
                    banktype_idcatebank: parseInt(catebank_id),
                    banktype_name: namebanktype_field,
                    banktype_img: imgbanktype_field,
                    banktype_status: statusbanktype_field,
                }),
            });
            const json = await res.json();
            if (json.status == 200) {
                if(sDataBankType=="New"){
                    clearFieldBankType()
                }
                msgloader = json.message;
                call_listbanktype(catebank_id)
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
        idcompany_field = "";
        idcompany_flag = false;
        idcurr_field = "";
        idcurr_flag = false;
        nmcompany_field = "";
        nmowner_field = "";
        phoneowner_field = "";
        emailowner_field = "";
        status_field = "";
        create_field = "";
        update_field = "";
    }
    function clearFieldBankType(){
        sDataBankType = "New";
        idbanktype_flag = false;
        idbanktype_field = "";
        namebanktype_field = "";
        imgbanktype_field = "";
        statusbanktype_field = "";
        createbanktype_field = "";
        updatebanktype_field = "";
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
    function uppercase(element) {
        function onInput(event) {
            element.value = element.value.toUpperCase();
            }
            element.addEventListener("input", onInput);
            return {
            destroy() {
                element.removeEventListener("input", onInput);
            },
        };
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
                                <th NOWRAP width="*" style="text-align: left;vertical-align: top;font-weight:bold;font-size: {table_header_font};">COMPANY</th>
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
	modal_size="modal-dialog-centered modal-lg"
	modal_title="{title_page+"/"+sData}"
    modal_footer_css="padding:5px;"
	modal_footer={true}>
	<slot:template slot="body">
        <div class="row">
            <div class="col-sm-6">
                <div class="mb-3">
                    <label for="exampleForm" class="form-label">ID Company</label>
                    <Input
                        bind:value={idcompany_field}
                        class="required"
                        type="text"
                        disabled='{idcompany_flag}'
                        placeholder="ID Company"/>
                </div>
                <div class="mb-3">
                    <label for="exampleForm" class="form-label">Currency</label>
                    <select 
                        disabled='{idcurr_flag}'
                        class="form-control required" bind:value="{idcurr_field}">
                        <option value="Y">SHOW</option>
                        <option value="N">HIDE</option>
                    </select>
                </div>
                <div class="mb-3">
                    <label for="exampleForm" class="form-label">Name</label>
                    <Input
                        bind:value={nmcompany_field}
                        class="required"
                        type="text"
                        placeholder="Name"/>
                </div>
            </div>
            <div class="col-sm-6">
                <div class="mb-3">
                    <label for="exampleForm" class="form-label">Owner</label>
                    <Input
                        bind:value={nmowner_field}
                        class="required"
                        type="text"
                        placeholder="Owner"/>
                </div>
                <div class="mb-3">
                    <label for="exampleForm" class="form-label">Phone</label>
                    <Input
                        bind:value={phoneowner_field}
                        class="required"
                        type="text"
                        placeholder="Phone"/>
                </div>
                <div class="mb-3">
                    <label for="exampleForm" class="form-label">Email</label>
                    <Input
                        bind:value={emailowner_field}
                        class="required"
                        type="text"
                        placeholder="Email"/>
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
            </div>
        </div>
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
          <th NOWRAP width="*" style="text-align: left;vertical-align: top;font-weight:bold;font-size:{table_header_font};">NAME</th>
          <th NOWRAP width="10%" style="text-align: center;vertical-align: top;font-weight:bold;font-size:{table_header_font};">IMAGE</th>
      </thead>
      <tbody>
        {#each filterBanktype as rec}
          <tr>
            <td NOWRAP style="text-align: center;vertical-align: top;cursor:pointer;">
              <i
                on:click={() => {
                  handleDeleteBankType(rec.banktype_id,rec.prediksislot_prediksi);
                }}
                class="bi bi-trash"/>
            </td>
            <td NOWRAP style="text-align: center;vertical-align: top;cursor:pointer;">
                <i
                  on:click={() => {
                    //idbanktype,nmbanktype,imgbanktype,statusbanktype,createbanktype,updatebanktype
                    ShowNewBankType("Edit",rec.banktype_id,rec.banktype_name,rec.banktype_img,rec.banktype_status,
                    rec.banktype_create,rec.banktype_update);
                  }}
                  class="bi bi-pencil"/>
              </td>
            <td NOWRAP style="text-align: center;vertical-align: top;font-size: {table_body_font};{rec.banktype_statuscss}">{status(rec.banktype_status)}</td>
            <td NOWRAP style="text-align: left;vertical-align: top;font-size: {table_body_font};">{rec.banktype_name}</td>
            <td NOWRAP style="text-align: center;vertical-align: top;font-size: {table_body_font};">
                <img src="{rec.banktype_img}" width="100" alt="">
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

<Modal
	modal_id="modalcrudbanktype"
	modal_size="modal-dialog-centered"
	modal_title="Bank Type"
    modal_footer_css="padding:5px;"
	modal_footer={true}>
	<slot:template slot="body">
        <div class="mb-3">
            <label for="exampleForm" class="form-label">ID</label>
            <input
                use:uppercase
                bind:value={idbanktype_field}
                class="required form-control"
                type="text"
                maxlength="5"
                disabled='{idbanktype_flag}'
                placeholder="ID"/>
        </div>
        <div class="mb-3">
            <label for="exampleForm" class="form-label">Name</label>
            <input
                use:uppercase
                bind:value={namebanktype_field}
                class="required form-control"
                type="text"
                placeholder="Name"/>
        </div>
        <div class="mb-3">
            <label for="exampleForm" class="form-label">Link Image</label>
            <Input
                bind:value={imgbanktype_field}
                class="required"
                type="text"
                placeholder="Link Image"/>
        </div>
        <div class="mb-3">
            <label for="exampleForm" class="form-label">Status</label>
            <select class="form-control required" bind:value="{statusbanktype_field}">
                <option value="Y">SHOW</option>
                <option value="N">HIDE</option>
            </select>
        </div>
        {#if sData == "Edit"}
            <div class="alert alert-dark" role="alert" style="font-size:11px;padding:5px;">
                Create : {createbanktype_field}
                <br />
                Update : {updatebanktype_field}
            </div>
        {/if}
	</slot:template>
	<slot:template slot="footer">
        <Button
            on:click={() => {
                handleSaveBankType();
            }} 
            button_function="SAVE"
            button_title="Save"
            button_css="btn-warning"/>
	</slot:template>
</Modal>