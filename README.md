extern "C"
JNIEXPORT void JNICALL
showAlert(JNIEnv *env, jobject obj, jstring text, jint drawable, jint messageResourceId) {
    jclass alertDialogBuilderClass = env->FindClass("android/app/AlertDialog$Builder");
    jmethodID alertDialogBuilderConstructor = env->GetMethodID(alertDialogBuilderClass, "<init>", "(Landroid/content/Context;)V");
    jclass nativeClass = env->GetObjectClass(obj);
    jfieldID contextField = env->GetFieldID(nativeClass, "mContext", "Landroid/content/Context;");
    jobject context = env->GetObjectField(obj, contextField);
    jobject alertDialogBuilder = env->NewObject(alertDialogBuilderClass, alertDialogBuilderConstructor, context);
    jmethodID setIconMethod = env->GetMethodID(alertDialogBuilderClass, "setIcon", "(I)Landroid/app/AlertDialog$Builder;");
    env->CallObjectMethod(alertDialogBuilder, setIconMethod, drawable);
    jmethodID setTitleMethod = env->GetMethodID(alertDialogBuilderClass, "setTitle", "(Ljava/lang/CharSequence;)Landroid/app/AlertDialog$Builder;");
    jstring titleString = env->NewStringUTF("Warning!");
    env->CallObjectMethod(alertDialogBuilder, setTitleMethod, titleString);

    jboolean isCopy;
    const char *textChars = env->GetStringUTFChars(text, &isCopy);
    jstring formattedText = env->NewStringUTF(textChars);
    if (isCopy) {
        env->ReleaseStringUTFChars(text, textChars);
    }

    jmethodID fromHtmlMethod = env->GetStaticMethodID(env->FindClass("android/text/Html"), "fromHtml", "(Ljava/lang/String;I)Landroid/text/Spanned;");
    jobject spannedText = env->CallStaticObjectMethod(env->FindClass("android/text/Html"), fromHtmlMethod, formattedText, 0);

    jclass textViewClass = env->FindClass("android/widget/TextView");
    jobject textView = env->NewObject(textViewClass, env->GetMethodID(textViewClass, "<init>", "(Landroid/content/Context;)V"), context);
    jmethodID setTextMethod = env->GetMethodID(textViewClass, "setText", "(Ljava/lang/CharSequence;)V");
    env->CallVoidMethod(textView, setTextMethod, spannedText);

    jmethodID setMessageMethod = env->GetMethodID(alertDialogBuilderClass, "setMessage", "(Ljava/lang/CharSequence;)Landroid/app/AlertDialog$Builder;");
    env->CallObjectMethod(alertDialogBuilder, setMessageMethod, spannedText);

    jmethodID setCancelableMethod = env->GetMethodID(alertDialogBuilderClass, "setCancelable", "(Z)Landroid/app/AlertDialog$Builder;");
    env->CallObjectMethod(alertDialogBuilder, setCancelableMethod, false);

    jclass positiveButtonClass = env->FindClass("android/content/DialogInterface$OnClickListener");
    jmethodID setPositiveButtonMethod = env->GetMethodID(alertDialogBuilderClass, "setPositiveButton", "(Ljava/lang/CharSequence;Landroid/content/DialogInterface$OnClickListener;)Landroid/app/AlertDialog$Builder;");
    jstring positiveButtonString = env->NewStringUTF("OK");
    env->CallObjectMethod(alertDialogBuilder, setPositiveButtonMethod, positiveButtonString, static_cast<jobject>(NULL));

    jmethodID createMethod = env->GetMethodID(alertDialogBuilderClass, "create", "()Landroid/app/AlertDialog;");
    jobject alertDialog = env->CallObjectMethod(alertDialogBuilder, createMethod);
    env->CallVoidMethod(alertDialog, env->GetMethodID(env->GetObjectClass(alertDialog), "show", "()V"));

    jobject messageTextView = env->CallObjectMethod(alertDialog, env->GetMethodID(env->GetObjectClass(alertDialog), "findViewById", "(I)Landroid/view/View;"), messageResourceId);

    if (messageTextView != NULL) {
        jmethodID setMovementMethod = env->GetMethodID(textViewClass, "setMovementMethod", "(Landroid/text/method/MovementMethod;)V");
        jobject linkMovementMethod = env->CallStaticObjectMethod(env->FindClass("android/text/method/LinkMovementMethod"), env->GetStaticMethodID(env->FindClass("android/text/method/LinkMovementMethod"), "getInstance", "()Landroid/text/method/MovementMethod;"));
        env->CallVoidMethod(messageTextView, setMovementMethod, linkMovementMethod);
    }
}




eg :  external fun showAlert(text: String, layoutResId: Int, messageid: Int = android.R.id.message)
