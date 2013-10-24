### Execution process in more details

* Initialization. `$f=$p->add();` is called, template is loaded and `form->init()` is automatically executed. Inside `init()` form's tempalte is separated into chunks.
* Field are inserted into forms with `addField();`.
* Optionally isSubmitted() is called.
    * `loadData()` is called. It's used when form is linked with database for data loading.
    * `submitted()` is called. Verifies if form was really submitted.
    * It calls `loadPOST()` for all fields recurrsively.
    * Calls `validate()` recursively. Any errors are accumulated in `$f->errors`.
    * `submitted()` returns true if didn't receive any validation errors.
* `isSubmitted()` returns true if data was submitted and contained no errors.
* Before rendering — if `isSubmitted()` wasn't used then `submitted()` method of respective form is called then.
* Execution is expected to be terminated when form submission is handled. If it wasn't done until rendering, `lateSubmit()` is called which output console warning about missing form handler.
* Global rendering starts which recursively renders fields first, then form.