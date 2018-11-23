---
layout: post
title:  "Android ReferenceActivity 设置列表笔记"
date:   2018-11-20
categories: notes android
---

PreferenceActivity无法单独使用,必须配合PreferenceFragment

__Activity中设置PreferenceFragment__

    <fragment
        android:id="@+id/fragment"
        android:name="com.viator42.simplemarkdown.module.settings.PreferenceGeneralFragment"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

__创建列表定义文件res/xml/pref_settings_general__



__PreferenceFragment.java__

    public class PreferenceGeneralFragment extends PreferenceFragment implements Preference.OnPreferenceChangeListener {
        private RefAction refAction;

        public PreferenceGeneralFragment() {
        }

        @Override
        public void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            addPreferencesFromResource(R.xml.pref_settings_general);

            if (refAction == null) {
                refAction = new RefAction(getActivity());
            }

            String value;

            ListPreference themeSettingPreference = (ListPreference) findPreference(getResources().getString(R.string.theme_setting_key));
            themeSettingPreference.setOnPreferenceChangeListener(this);
            value = refAction.getSettings(getResources().getString(R.string.theme_setting_key));
            if(!CommonUtils.isStrEmpty(value)) {
                themeSettingPreference.setValue(value);
            }

            ListPreference displayModeSettingPreference = (ListPreference) findPreference(getResources().getString(R.string.display_mode_setting_key));
            displayModeSettingPreference.setOnPreferenceChangeListener(this);
            value = refAction.getSettings(getResources().getString(R.string.display_mode_setting_key));
            if(!CommonUtils.isStrEmpty(value)) {
                displayModeSettingPreference.setValue(value);
            }

            ListPreference fontsizeSettingPreference = (ListPreference) findPreference(getResources().getString(R.string.fontsize_setting_key));
            fontsizeSettingPreference.setOnPreferenceChangeListener(this);
            value = refAction.getSettings(getResources().getString(R.string.fontsize_setting_key));
            if(!CommonUtils.isStrEmpty(value)) {
                fontsizeSettingPreference.setValue(value);
            }


            HeadimgPreference headimgPreference = (HeadimgPreference) findPreference(getResources().getString(R.string.headimg_setting_key));
            headimgPreference.setOnPreferenceClickListener(new Preference.OnPreferenceClickListener() {
                @Override
                public boolean onPreferenceClick(Preference preference) {
                    //选择头像
                    return true;
                }
            });
            headimgPreference.setHeadimg(); //设置头像

        }

        @Override
        public boolean onPreferenceChange(Preference preference, Object newValue) {
            refAction.setSettings(preference.getKey(), newValue.toString());
            return true;
        }
    }

--------

# 自定义Reference Item




