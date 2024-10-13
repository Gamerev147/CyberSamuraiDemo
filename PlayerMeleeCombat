private void Attack(Transform target)
    {
        //(0.2f) is spam protection, might need to base this per animation/attackSO
        if (Time.time - _lastComboEnd > comboTimeout && _comboCounter <= combo.Count)
        {
            CancelInvoke("EndCombo");

            var clipInfo = _animator.GetCurrentAnimatorClipInfo(0);
            float clipLength = clipInfo.Length;

            if (Time.time - _lastClickedTime >= clipLength - 0.5f)
            {
                weaponObject.SetActive(true);
                _playerController.EnableRootMotion();
                _animator.runtimeAnimatorController = combo[_comboCounter].animatorOverride;
                _animator.Play("Attack", 0, combo[_comboCounter].animationCutTime);

                if (target != null) MoveToTarget(target, 0.2f);

                //Damage for non-sword attack
                if (!combo[_comboCounter].swordAttack)
                {
                    Invoke("PerformAttackDamage", combo[_comboCounter].attackTimeOffset);
                }

                //Damage for sword attack
                if (combo[_comboCounter].swordAttack && !combo[_comboCounter].finisherAttack) //not a finisher attack
                {
                    _attackType = combo[_comboCounter].attackType;
                    StartCoroutine(ManageWeaponCollider(combo[_comboCounter].colliderOpenTime, combo[_comboCounter].colliderCloseTime));
                } else if (combo[_comboCounter].swordAttack && combo[_comboCounter].finisherAttack) //is a finisher attack
                {
                    _finisherType = combo[_comboCounter].finisherType;
                    if (target != null)
                    {
                        target.position = transform.position + transform.forward * combo[_comboCounter].finisherAttackOffset;
                        TestEnemy currentEnemy = target.GetComponent<TestEnemy>();
                        if (currentEnemy != null)
                        {
                            currentEnemy.OnFinisherAttack(_finisherType);
                        }
                    }
                }

                //Time dilation effect
                float timeSlowChance = Random.Range(0f, 100f);
                if (combo[_comboCounter].chanceToSlow >= timeSlowChance)
                {
                    StartCoroutine(ManageTimeDilation(combo[_comboCounter].startTimeSlow, combo[_comboCounter].endTimeSlow, combo[_comboCounter].timeSlowFactor));
                }

                //Hand effects
                if (combo[_comboCounter].leftHandEffect)
                {
                    if (combo[_comboCounter].allowHandEffects) leftHandEffect.Play();
                } else
                {
                    if (combo[_comboCounter].allowHandEffects) rightHandEffect.Play();
                }

                //Sound effects
                if (combo[_comboCounter].swordAttack)
                {
                    if (combo[_comboCounter].chanceToSlow >= timeSlowChance)
                    {
                        AudioClip clip = swingSounds[Random.Range(0, swingSounds.Length)];
                        AudioController.Instance.PlayClip(clip, 0.8f, combo[_comboCounter].timeSlowFactor + 0.1f);
                    } else
                    {
                        AudioClip clip = swingSounds[Random.Range(0, swingSounds.Length)];
                        AudioController.Instance.PlayClip(clip, 0.8f);
                    }
                }
                else
                {
                    AudioClip clip = punchSounds[Random.Range(0, punchSounds.Length)];
                    _audioSource.PlayOneShot(clip, 0.5f);
                }

                //Set damage and force
                _damage = combo[_comboCounter].damage;
                _force = combo[_comboCounter].force;

                _comboCounter++;
                _lastClickedTime = Time.time;

                if (_comboCounter + 1 > combo.Count)
                {
                    _comboCounter = 0;
                }
            }
        }
    }
